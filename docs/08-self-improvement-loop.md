# 08 — The Self-Improvement Loop

A harness should get *better over time*, not just run. This page describes the flywheel that
keeps an agent system (and this repository itself) improving, grounded in OpenAI's
*Agent Improvement Loop* (traces + evals + Codex) and Anthropic's eval-driven practice. It is
also the design behind this repo's own weekly maintenance cycle — see
[`../SELF-IMPROVEMENT.md`](../SELF-IMPROVEMENT.md).

## The flywheel

```
   ┌──────────────────────────────────────────────────────────────┐
   │                                                              │
   ▼                                                              │
 TRACES ──▶ FEEDBACK ──▶ EVALS ──▶ VALIDATION ──▶ OPTIMIZE ──▶ HANDOFF
 (what     (human +      (codify   GATE          (rank the    (implement
  happened) model        expectations  (baseline,  improvements  the change;
            critique)    as reruns)  no regress)  by impact)   review or auto)
```

The discipline: **traces + human and model feedback become concrete harness changes, instead
of remaining disconnected comments.** Each cycle's learning becomes reusable evidence for the
next.

### Stage 1 — Traces
Capture the full execution path of real runs: agent decisions, model generations, tool calls,
handoffs, and guardrail triggers. (OpenAI's Agents SDK captures these automatically across
those five dimensions.) Traces are the ground truth the rest of the loop reasons over.

### Stage 2 — Feedback
Human reviewers and an LLM critic annotate traces with what succeeded or failed. This bridges
raw execution data and actionable signal. Keep the feedback *coupled* to the trace it explains.

### Stage 3 — Evals
Convert feedback into **reusable evaluation suites** that can be rerun later. These are
regression gates: they confirm a new version still passes previously identified issues while
measuring improvement. Evals for agents measure trajectories and outcomes, not just final
strings (Anthropic, *Demystifying Evals*).

### Stage 4 — Validation gate
Run the evals against the **current** harness first to establish a baseline and prevent
regressions before any change is proposed.

### Stage 5 — Optimize
Analyze the full dataset (traces + feedback + eval results) and **rank candidate improvements
by impact**, each backed by an evidence chain. Treat the *whole harness* as the optimization
unit — system instructions, tool policies, routing rules, output requirements, validation
checks — not just the prompt.

### Stage 6 — Handoff
Write a developer-facing handoff (e.g. `codex_handoff.md`) carrying the diagnosis, ranked
recommendations, evidence, and implementation guidance an implementing agent needs to make the
change.

## The research backs the flywheel

This isn't just a vendor pattern — it's now a measured result. *Agentic Harness Engineering*
(arXiv:2604.25850) runs exactly this loop autonomously over three **observability pillars**:

- **Component observability** — every editable harness component has a file-level
  representation, so the action space is explicit and **revertible**.
- **Experience observability** — trajectory data is distilled into a layered, drill-down
  evidence corpus the evolving agent can actually consume (the "traces + feedback" stages).
- **Decision observability** — **each edit is paired with a self-declared prediction, later
  verified against the next round's outcome.** This turns every change into a *falsifiable
  contract*, so evolution proceeds without collapsing into trial-and-error.

That last point is the rigorous form of the **validation gate**: don't just propose a change —
predict its effect and check the prediction next cycle. Results: Terminal-Bench 2 Pass@1
**69.7% → 77.0%** over ten iterations (beating the human-designed Codex-CLI at 71.9%), with
**+5.1 to +10.1 pp** gains transferring across three other model families. The ablation is the
key lesson: **factual harness structure transfers; prose-level strategy does not** — gains come
from tools, middleware, and memory, *not* from rewording the system prompt. Optimize structure,
not vibes.

And because harness scores are confounded with the harness itself, measure on a harness-aware
benchmark: *Harness-Bench* (arXiv:2605.27922) shows a score reflects "not only what the model
can infer, but what the harness enables it to observe, modify, recover from, and verify."

## Two trust levels

| Level | Flow | Use when |
|---|---|---|
| **Reviewed loop** | System proposes a change set → human reviews/approves → merge | Starting point; eval gate not yet trusted |
| **Deeper automation** | Loop opens, validates, merges, and deploys a PR automatically | Once the eval gate is trusted |

Start reviewed. Earn automation by proving the eval gate catches regressions. This is the same
"gate irreversible actions, then relax as confidence grows" principle from
[`06-failure-modes.md`](06-failure-modes.md) (approval fatigue), applied to *self-modification*.

## Applying the flywheel to a knowledge repo (like this one)

This repo has no runtime traces, so the loop adapts: the "traces" are **the state of the field**
(new authoritative publications), and the "evals" are **freshness and authority checks**.

| Generic stage | This repo's analogue |
|---|---|
| Traces | Weekly scan of authoritative lab sources (Anthropic, OpenAI, Google, Microsoft, Meta) + new arXiv |
| Feedback | Diff against current docs: what's new, what's now stale, what's contradicted |
| Evals | Freshness (no source older than its successor), authority (lab/first-party preferred), link liveness, internal-link integrity |
| Validation gate | Don't propose a change that breaks an existing claim's citation or a cross-link |
| Optimize | Rank: new primitive with quantified result > restatement > link fix |
| Handoff | A PR with the ranked changes and the source links as evidence |

The operational checklist lives in [`../SELF-IMPROVEMENT.md`](../SELF-IMPROVEMENT.md); the
weekly automation that runs it is described there.

Sources: see [`../references.md`](../references.md) §Self-Improvement.
