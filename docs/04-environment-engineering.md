# 04 — Environment Engineering

The outermost and least-appreciated layer. The **environment** is the world the agent
perceives and acts on — its filesystem, its tools' return values, its tests, its runtime, the
services it touches. For autonomous and long-horizon work, shaping the environment is often a
*larger* lever than either the prompt or the model.

The thesis in one title: *EurekAgent: **Agent Environment Engineering is All You Need** for
Autonomous Scientific Discovery* (arXiv:2606.13662). When an agent must discover, iterate, and
self-correct without a human in the loop, the quality of its environment — how legible its
state is and how fast and honest its feedback is — dominates outcomes.

## Principle 1 — Make state legible (filesystem-as-context)

Expose state as **files the agent can `read`, `grep`, `find`, and `write`**, rather than
hiding it behind large bespoke toolsets. The evidence is blunt:

- **Azure SRE Agent:** shifting from 100+ bespoke tools and a prescriptive prompt to a
  filesystem-based context system — exposing source code, runbooks, query schemas, and past
  investigation notes as files — raised the **"Intent Met" score from 45% to 75%** on novel
  incidents (Microsoft, *Context Engineering for Reliable AI Agents*).
- LLMs are already fluent in `grep`, `cat`, `cp`. Leaning on that vocabulary (`Mirage` mounts
  S3/Slack/Gmail/GitHub/Redis as one virtual filesystem) eliminates tool-schema bloat and lets
  cross-service pipelines compose like shell scripts.
- The filesystem is also a **durable collaboration surface** between agent sessions and
  between cooperating agents (LangChain, *Anatomy of an Agent Harness*).

## Principle 2 — Make feedback fast and honest

The environment should give the agent a **grounded signal** every iteration. Ranked by trust:

1. **Computational / ground truth** — a failing test, a compiler error, a type check, a
   runtime metric. Deterministic and cheap to trust.
2. **Retrieved evidence** — fresh, citable data.
3. **Inferential** — an LLM-as-judge. Useful where no computational signal exists, but weaker.

Prefer computational feedback whenever it's available (Böckeler). Wire tests, linters, and
metrics **directly into the loop** so the agent corrects before output reaches you. Red Hat's
framing: expand the agent's toolbox through MCP integrations (CI status, deployment logs,
runtime metrics) so these become *real data sources*, not described state.

## Principle 3 — Match runtime persistence semantics

Whether interpreter/tool **state persists between calls** is a learned semantic, not a free
runtime choice (*Agents Learn Their Runtime*, arXiv:2603.01209):

- Model expects persistence, environment doesn't provide it → ~80% missing-variable errors.
- Environment persists, model expects fresh state → ~3.5× redundant-recompute token overhead.

Design the execution environment to honor what the model was trained to expect. Likewise,
inject **temporal context** (current time, deadlines, time budgets) into the environment —
temporal awareness is orthogonal to reasoning capability and must be supplied, not assumed
(arXiv:2601.13206).

## Principle 4 — Treat the environment as a tunable, auditable artifact

The environment contract should be explicit and improvable, not implicit. Patterns:

- **Environment contract layers** (`Life-Harness`): an explicit environment contract +
  procedural skills + action realization + trajectory regulation. Harness-side adaptation of
  the model↔environment interface transferred across **18 model backbones** — strong evidence
  that many "agent failures" are interface mismatches, not reasoning deficits.
- **Agent-friendly surfaces**: serve `text/markdown` to agents via content negotiation
  (Vercel) so the environment hands the agent clean inputs instead of HTML boilerplate.
- **Entropy management**: schedule periodic agents that repair documentation/state drift in the
  environment (Fowler, *Harness Engineering*) — the environment decays without maintenance.

## Principle 5 — Isolate and sandbox

A sandbox provides both **safety** (blast-radius containment) and **verification** (a clean
place to run and check the agent's work) (LangChain). Ephemeral runners, firewall allowlists,
and isolated worktrees let the agent act freely without risking the real system
(GitHub, *Governing Agents*).

## How this composes with the inner layers

| Layer | What you tune | Dominant signal |
|---|---|---|
| Environment | legibility, feedback speed/honesty, persistence, sandbox | ground truth |
| Harness | tools, context mgmt, control, the loop | configuration |
| Loop | which feedback cycle, when to stop | the chosen critic |

The practical sequence for a hard autonomous task: **first** make the environment legible and
its feedback fast and honest; **then** structure the harness around it; **then** pick the
tightest loop the feedback can support. Reaching for a bigger model before doing those is
usually the wrong first move.

Sources: see [`../references.md`](../references.md) §Environment Engineering.
