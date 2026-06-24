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
