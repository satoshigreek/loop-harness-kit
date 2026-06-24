# 02 — Harness Engineering

This page is the design reference for the layer around the model. It synthesizes the 2026
practitioner literature (OpenAI Codex, Anthropic, LangChain, Microsoft, Google, Meta, Red Hat)
and the systematic papers into actionable primitives.

## The constitutive definition

A harness is a **runtime layer** with four necessary and sufficient elements
(*What makes a harness a harness*, arXiv:2606.10106):

1. **Agent loop** — the observe→plan→act→verify cycle with explicit termination.
2. **Tool interface** — how the model perceives and acts on the world.
3. **Context management** — what enters the window and how it's compacted.
4. **Control mechanisms** — permissions, gates, hooks, stopping rules.

Applied to Claude Code, Codex CLI, Aider, Cline, OpenHands, and SWE-agent, all four elements
are present. A system missing one is a generator, a guardrail, or a plain tool wrapper. Use
this as an inclusion test when evaluating any "agent framework."

## Decouple the brain from the hands (stable interfaces)

Anthropic's *Scaling Managed Agents* gives the sharpest architectural pattern for durable
harnesses: **separate the reasoning ("brain") from the execution environments ("hands") and
the session log** — the way operating systems virtualized hardware behind stable abstractions,
so implementations can change while interfaces stay constant.

- **The session is not the context window.** It's a durable, append-only event log stored
  *outside* the model's context, which the harness interrogates (e.g. `getEvents()`). The
  harness then performs arbitrary context transformations (compaction, trimming, cache
  organization) on top. Because the two are decoupled, a future harness can change its context
  strategy without breaking the session contract.
- **Each "hand" is a tool** with a uniform signature like `execute(name, input) → string`;
  credentials live in vaults/proxies outside the sandbox.
- **Stateless recovery:** `wake(sessionId)` / `getSession()` let a harness resume after a
  crash — the backbone of long-horizon and hibernate-and-wake work
  ([`07-lifecycle-and-artifacts.md`](07-lifecycle-and-artifacts.md)).

The payoff is concrete: decoupling cut **p50 time-to-first-token ~60%** and **p95 >90%**.

**Why stable interfaces matter — the expiry principle, made concrete.** A harness "encodes
assumptions about what the model can't do," and those assumptions go stale. Anthropic's own
example: Claude Sonnet 4.5 showed *context anxiety* (wrapping up tasks prematurely), so
engineers added context resets — which became **dead weight** with Claude Opus 4.5, where the
behavior had vanished. Keep such workarounds behind a stable interface so you can delete them
without churning the rest of the system. Treat every piece of scaffolding as removable.

## Design primitives, organized by the problem they solve

### Tool design (the model's UX)

Tool design *is* agent UX (Anthropic, *Writing Effective Tools for Agents*). Principles:

- **Name and scope tools clearly**; prefer a few sharp tools over many overlapping ones.
- **Make errors instructive** — the error surface is how the model learns to change approach.
  Read a denied/failed call as a signal to adapt, not to retry verbatim.
- **Annotate risk.** MCP's four hints — `readOnlyHint`, `destructiveHint`, `idempotentHint`,
  `openWorldHint` — feed permission decisions. Watch the **lethal trifecta**: private-data
  access + untrusted-content exposure + external communication. The danger emerges from tool
  *combinations*, not single tools.
- **Constrain topology, not just availability.** *How* tools chain and branch (`TopoCurate`,
  arXiv:2603.01714) matters as much as which tools exist.

### Advanced tool use (now GA on the Claude Developer Platform)

Three first-party primitives turn tool design from "list every tool" into a context-aware
discipline (Anthropic, *Introducing advanced tool use*). Each ships with a measured win:

| Primitive | What it does | Measured effect |
|---|---|---|
| **Tool Search** | Claude discovers tools on demand instead of loading all definitions upfront | **−85% tool-definition tokens**; Opus 4.5 tool accuracy **79.5% → 88.1%** |
| **Programmatic Tool Calling** | Claude orchestrates tools by writing code; intermediate results stay out of context | **−37% tokens** on complex research (43,588 → 27,297); powers Claude for Excel over thousands of rows |
| **Tool Use Examples** | Sample invocations inside the tool definition show real parameter usage JSON Schema can't | accuracy **72% → 90%** on complex parameter handling |

The unifying problem they solve: *intermediate results pile up in context*. Pair them with the
memory tool (write context to files for cross-session learning) and context editing/trimming
(see [`03-context-engineering.md`](03-context-engineering.md)).

### Think in code (collapse tool-call overhead)

When tool calls multiply, wrap multi-step interaction in **code execution** rather than
exposing each step as a discrete tool call. Anthropic measured **up to 98.7% token reduction**
doing this with MCP. Related patterns:

- Navigate codebases by **symbol/pointer** (AST index), not by reading whole files —
  `token-savior`, `codebase-memory-mcp`, and `semble` report 77–98% active-token cuts.
- Sandbox bulky tool output **outside** the window and retrieve only relevant fragments
  (`context-mode`, `headroom`).

### Planning & decomposition

Separate **planning** from **execution** as distinct harness layers (LangChain, *Plan-and-
Execute*; `microsoft/TaskWeaver`). A planner LLM produces the step list once; an executor works
through it, replanning only when needed. Specialize each layer independently — different model
sizes, tool access, and reasoning budgets for planner vs. executor (*Plan-and-Act*,
arXiv:2503.09572; *Task-Decoupled Planning*, arXiv:2601.07577).

For multi-agent topologies, match the topology to the task's dependency graph
(`AdaptOrch`, arXiv:2602.16873 — 12–23% gains from topology choice over model choice). And
treat every handoff like a distributed-systems boundary: **typed schemas, constrained action
schemas, explicit boundary validation** (GitHub, *Multi-Agent Workflows Often Fail*).

### Control mechanisms

- **Hooks** at lifecycle events (`SessionStart`/`PreToolUse`/`PostToolUse`) enforce
  deterministic policy the prompt can't be trusted to remember.
- **State-machine guardrails** constrain which tools are legal per phase. `statewright` took a
  local model from 2/10 to 10/10 on a SWE-bench subset by shrinking the tool space alone.
- **Loop-detection middleware** breaks repeated-state cycles.
- **Permissions** evaluated in a defined order (Claude Agent SDK: hooks → deny rules →
  permission mode → allow rules → `canUseTool`). Gate only irreversible/high-risk actions to
  avoid approval fatigue (see [`06-failure-modes.md`](06-failure-modes.md)).

### Verification & evals

Wire verification into the loop, and prefer **computational** controls (linters, tests,
type checks) over **inferential** ones (LLM-as-judge) whenever a computational signal exists
(Böckeler, *Harness engineering for coding agent users*). Build eval gates that **block
deployment** and observability that tracks every agent decision (*LLM Readiness Harness*,
arXiv:2603.27355). Evals for agents are not unit tests — measure trajectories and outcomes,
not just final strings (Anthropic, *Demystifying Evals for AI Agents*).

## Lessons from production harnesses (OpenDev, arXiv:2603.05344)

The first systematic practitioner paper on terminal-native coding-agent harnesses distills
five lessons that generalize to any server-side agent:

1. **Eager construction** — pre-build all harness components *before* the first message, to
   eliminate first-call latency and race conditions.
2. **Compound multi-model architecture** — use different model instances for execution,
   reasoning, critique, and vision rather than one model for everything.
3. **Defense in depth** — layered safety (they use five layers), not a single guard.
4. **Schema-filtered planning subagents** — enforce behavioral constraints via the *tool
   schema* the subagent is given, rather than runtime permission checks after the fact.
5. **Harness assumptions expire** — revisit scaffolding as models improve.

## Make the *repository* harness-ready

Coding agents need better repositories, not just better prompts. Externalize the harness into
versioned files the agent reads on entry:

- **`AGENTS.md` / `CLAUDE.md`** — durable operating rules (survive compaction).
- **`HARNESS.md` / `FEATURE_INTAKE.md`** — where to start, the product contract, change risk,
  decisions future agents should inherit (`harness-experimental`).
- Portable `.agent/` configuration with adapters across tools (`agentic-stack`, `Trellis`,
  `superpowers`) to dodge vendor lock-in.

## The headline evidence

| Change (no model swap) | Result | Source |
|---|---|---|
| Structured verification + context injection + loop detection | rank 30 → top 5, Terminal Bench 2.0 | LangChain |
| Filesystem context vs. 100+ bespoke tools | Intent Met 45% → 75% | Azure SRE Agent |
| Shrinking legal tool space per phase | 2/10 → 10/10, SWE-bench subset | `statewright` |
| Harness-only tuning | +20 ranking positions | deepset |
| Harness setup as a variable | ±5 benchmark points | Anthropic Trends Report |

Sources: see [`../references.md`](../references.md) §Harness Engineering and §Design Primitives.
