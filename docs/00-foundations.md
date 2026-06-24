# 00 — Foundations: Loop, Harness, Environment

Three distinct but nested layers determine whether an LLM agent succeeds on real work. They
are often conflated. Keeping them separate is the first move of the discipline.

```
┌─────────────────────────────────────────────────────────┐
│  ENVIRONMENT  — the world the agent acts in              │
│   (files, tests, runtimes, services, ground-truth signal)│
│  ┌───────────────────────────────────────────────────┐  │
│  │  HARNESS  — the runtime layer around the model     │  │
│  │   (context mgmt, tools, control, the loop itself)  │  │
│  │  ┌─────────────────────────────────────────────┐  │  │
│  │  │  LOOP  — the feedback cycle the model runs    │  │  │
│  │  │   (observe → plan → act → verify → repeat)    │  │  │
│  │  │            ┌──────────────┐                   │  │  │
│  │  │            │    MODEL      │                   │  │  │
│  │  │            └──────────────┘                   │  │  │
│  │  └─────────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

## Loop engineering

A **loop** is a feedback cycle: the model produces something, gets a signal about it, and
revises. Loop engineering is choosing *which* loop to run and *when not to run one at all*.

The central rule, from Anthropic's *Building Effective Agents*: **only add a loop when a
single well-prompted pass falls short.** A loop is only worth its cost if it injects a
**grounded feedback signal** — something external to the model's own confidence. Ranked
strongest to weakest: environment ground truth (tests, compilers, tool output) → retrieved
evidence → an independent critic → an explicit rubric → bare self-critique. Self-critique with
no fresh signal improves *polish*, not *correctness*.

The catalog of loops (Self-Consistency, ReAct, Reflexion, Tree of Thoughts, Orchestrator-
Worker, and a dozen more) lives in [`05-loop-patterns.md`](05-loop-patterns.md) and, visually,
in [`../reference/ai-loops-reference.html`](../reference/ai-loops-reference.html).

## Harness engineering

> *Harness engineering* is the discipline of designing the scaffolding — context delivery,
> tool interfaces, planning artifacts, verification loops, memory systems, and sandboxes —
> that surrounds an AI agent and determines whether it succeeds or fails on real tasks.
> — *Awesome Harness Engineering*

The harness is everything around the model that isn't the model. The defining 2026 result is
that **the harness, not the model, is the primary performance lever**:

- LangChain moved a coding agent **from rank 30 to top 5 on Terminal Bench 2.0 with no model
  swap** — purely via structured verification loops, directory-map context injection, loop
  detection, and a "reasoning sandwich."
- deepset showed harness-only changes can move an agent **20+ ranking positions**.
- Anthropic's *2026 Agentic Coding Trends Report* found **harness setup alone can swing
  benchmarks by 5+ percentage points**.
- `statewright` took a local model from **2/10 to 10/10** on a SWE-bench subset purely by
  shrinking the legal tool space per phase.
- *Harness-Bench* (arXiv:2605.27922) formalizes the **binding-constraint thesis**: in
  realistic agent workflows a benchmark score measures "not only what the model can infer, but
  what the harness enables it to observe, modify, recover from, and verify" — so for
  long-horizon tasks, variance is driven as much by the harness as by the model.

A useful constitutive test (*"What makes a harness a harness"*, arXiv:2606.10106): a true
harness has **four necessary and sufficient elements** — an agent loop, a tool interface,
context management, and control mechanisms. Miss one and you have a generator, a guardrail, or
a tool wrapper — not a harness. Detail in [`02-harness-engineering.md`](02-harness-engineering.md).

**The expiry principle** (Anthropic): every harness component exists because the model can't
do something *yet*. As models improve, some scaffolding becomes unnecessary. Design knowing
your scaffolding has a shelf life — don't hard-code around limitations you'll want to remove.

## Environment engineering

The outermost layer is the **environment**: the world the agent perceives and acts on. For
autonomous and discovery-style tasks, shaping the environment is the dominant lever — captured
in the title of *EurekAgent* (arXiv:2606.13662): **"Agent Environment Engineering is All You
Need for Autonomous Scientific Discovery."**

The core moves: make state **legible** (expose it as files the agent can read/grep/write),
make feedback **fast and honest** (tests, type checks, metrics wired into the loop), and match
the **runtime semantics** the model was trained to expect. Microsoft's Azure SRE Agent raised
its "Intent Met" score **from 45% to 75%** on novel incidents simply by exposing everything as
files and letting the agent use `read_file`, `grep`, `find`, and `shell` instead of 100+
bespoke tools. Detail in [`04-environment-engineering.md`](04-environment-engineering.md).

## Why this matters for *you* (the human pointing an LLM here)

When your agent underperforms, the instinct is to reach for a better model or a longer prompt.
The evidence above says reach for the harness first: tighten the loop, curate the context,
wire in a real verification signal, constrain the tool space, and re-anchor to intent. That is
what the rest of this repo equips your agent to do.

Sources for this page: see [`../references.md`](../references.md) §Foundations.
