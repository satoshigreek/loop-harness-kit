# 01 — The Agent Loop

The agent loop is the heartbeat of every harness. ~60% of 70 surveyed open-source agent
projects adopt it as their core control pattern (*A Scheduler-Theoretic Framework for LLM
Agent Execution*, arXiv:2604.11378). Understanding its anatomy tells you where every other
harness component plugs in.

## One iteration, unrolled

The canonical decomposition of a single loop iteration (OpenAI, *Unrolling the Codex Agent
Loop*; grounded in *ReAct*, arXiv:2210.03629):

```
        ┌──────────────────────────────────────────────┐
        │                                              │
        ▼                                              │
   ┌─────────┐   ┌────────┐   ┌────────┐   ┌─────────┐  │
   │ OBSERVE │ → │  PLAN  │ → │  ACT   │ → │ VERIFY  │ ─┘
   │ (read   │   │(reason │   │(call a │   │(check   │
   │  state, │   │ about  │   │ tool / │   │ against │
   │  tool   │   │ next   │   │ emit   │   │ a       │
   │  output)│   │ step)  │   │ output)│   │ signal) │
   └─────────┘   └────────┘   └────────┘   └─────────┘
                                                │
                                       ┌────────┴────────┐
                                       │ termination?    │
                                       │ (guard met,     │
                                       │  budget spent,  │
                                       │  evals pass)    │
                                       └────────┬────────┘
                                            yes │  → STOP
```

ReAct's contribution was making the **Thought / Action / Observation** interleave explicit:
the model reasons, acts, observes the result, and reasons again — rather than planning blind.
Every modern harness is a variation on this skeleton.

## Where harness components attach

| Loop stage | Harness components that attach here |
|---|---|
| **Observe** | context delivery, compaction, retrieval-as-tool, memory recall |
| **Plan** | planning artifacts (`PLAN.md`), task decomposition, extended thinking budget |
| **Act** | tool interface, permissions/authorization, hooks (`PreToolUse`) |
| **Verify** | tests, linters, LLM-as-judge, eval gates, hooks (`PostToolUse`) |
| **Terminate** | stopping rules, iteration caps, loop-detection middleware |

Middleware frameworks expose these as concrete interception points. LangChain's `AgentMiddleware`
gives six composable hooks — `before_agent`, `before_model`, `wrap_model_call`, `wrap_tool_call`,
`after_model`, `after_agent` — and OpenAI's Codex exposes lifecycle hooks at `SessionStart`,
`PreToolUse`, `PostToolUse`, etc. These let you enforce deterministic policy (PII redaction,
audit, retries, HITL interrupts) *without* trusting the prompt to remember.

## The reasoning sandwich

Reasoning effort is not free, and it does not pay off evenly across the loop. LangChain's
Terminal Bench result used a **"reasoning sandwich"**: concentrate the model's deepest thinking
at the **planning** stage (before any action) and the **verification** stage (before
termination), and act cheaply in between. This is the highest-leverage place to spend
extended-thinking tokens.

> Practical: if your harness supports a per-turn thinking budget, spend it at plan and verify,
> not on every routine tool call.

## Termination is a first-class design problem

The most common loop failure is **not terminating well** — either running away (cost compounds,
the agent thrashes) or stopping early (quitting before evals pass). Build the stopping rule
explicitly:

- **Guard-based:** stop when an acceptance condition is met (tests green, rubric satisfied).
- **Budget-based:** cap iterations (3–4 is the empirical sweet spot) and token/time budgets.
- **Loop-detection:** detect repeated states/actions and break out — LangChain shipped loop
  detection as middleware specifically because agents get stuck in cycles.

Stop the moment your evals pass. More rounds rarely help and reliably cost more.

## Persistence semantics (a subtle but expensive detail)

Whether your runtime **persists interpreter/tool state between calls** is not a free choice —
it's a *learned semantic* the model was trained with (*Agents Learn Their Runtime*,
arXiv:2603.01209). Mismatch it and you get one of two failures:

- Model expects state to persist but it doesn't → ~80% missing-variable errors.
- Model expects fresh state but it persists → ~3.5× token overhead from redundant recomputation.

Honor the model's expected persistence mode when you design the execution environment.

## Loop architectures beyond the basic agent loop

The basic agent loop is one of several execution patterns. Choose deliberately
(arXiv:2604.11378):

- **Agent Loop** — the default; observe→act until done.
- **Event-driven** — react to events as they arrive (needed for streaming/real-time).
- **State-machine** — guarded transitions between discrete phases (maximizes control).
- **Graph/flow** — typed state, conditional edges, checkpointing (e.g. LangGraph).
- **Hybrid** — combinations of the above.

The trade-off axis is **controllability vs. expressiveness vs. implementability**. More
structure (state machines, graphs) buys determinism and safety at the cost of flexibility.
For tool-checkable work where reliability matters, bias toward more structure.

Sources: see [`../references.md`](../references.md) §Agent Loop.
