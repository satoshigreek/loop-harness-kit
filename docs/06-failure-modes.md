# 06 — Failure Modes & Their Harness Fixes

Long-horizon agent runs fail in characteristic, repeatable ways. Each failure maps to a
specific missing or weak harness component (deepset's framing: context failures, constraint
failures, verification failures, planning failures). Diagnose by symptom; fix at the harness
layer, not by "trying harder."

---

## Semantic drift — *the headline failure*

**Symptom:** over many turns, the output slowly diverges from the original intent. Each step is
locally reasonable, but the cumulative trajectory wanders away from what was actually asked.
The agent ends up confidently solving a subtly different problem.

**Why it happens:** the model anchors on its *most recent* output rather than the *original
requirement*. Compaction quietly drops the initial instructions (see
[`03-context-engineering.md`](03-context-engineering.md)). Each iteration's small reinterpretation
compounds.

**Harness fixes:**
1. **Persist the intent as an artifact.** Write the goal and acceptance criteria to a durable
   file (`PLAN.md`) that lives outside the scrollback and survives compaction.
2. **Re-anchor every iteration.** At the top of each loop, restate the original requirement and
   acceptance criteria *before* generating. Verify against the original spec, **not** the last
   draft.
3. **Make the critic check intent, not polish.** An isolated reviewer's job is "does this still
   satisfy the original requirement?" — not "is this prose nicer?"
4. **Cap iterations.** Drift accumulates with turn count; fewer, better-grounded rounds drift
   less. Stop the moment acceptance criteria are met.
5. **Structure the loop** (state machine / graph) so phase transitions are guarded by the spec
   rather than by the model's momentum.

> Semantic drift is why "ground the critique" and "re-anchor to intent" are cross-cutting
> rules. A loop with no fresh, intent-referenced signal *amplifies* drift instead of correcting
> it — it polishes the agent's confidence in the wrong direction.

---

## Context rot

**Symptom:** quality declines as the window fills — worse instruction-following, forgotten
constraints — with no error to flag it.

**Fix:** proactive compaction between subtasks; navigate by symbol/pointer; sandbox bulky tool
output; keep durable rules in a persistent file. Full treatment in
[`03-context-engineering.md`](03-context-engineering.md).

---

## Self-praise / same-context review

**Symptom:** the agent "reviews" its own work in the same context and approves it. Real defects
sail through.

**Fix:** **isolate the critic** — a fresh-context reviewer or a different model/role. This is
the single highest-value upgrade to any critique loop (Generate-then-Review Subagent pattern).

---

## Runaway iteration / non-termination

**Symptom:** the loop never converges; the agent thrashes between near-identical states; cost
compounds.

**Fix:** explicit stopping rules — guard-based (stop when evals pass), budget-based (cap at 3–4
iterations and a token/time ceiling), and **loop-detection middleware** that breaks repeated-
state cycles. Termination is a first-class design problem
([`01-the-agent-loop.md`](01-the-agent-loop.md)).

---

## Approval fatigue

**Symptom:** every action is gated for human approval; users approve ~93% of prompts, so
approvals become meaningless rubber-stamps and real risks slip through.

**Fix:** gate only **irreversible / high-risk** actions; deny-and-continue on the rest. A
two-stage classifier (fast single-token gate, reasoning only on flagged actions) preserves
safety without fatigue (Anthropic, *Claude Code Auto Mode*). Strip assistant messages before
the safety check so the agent can't rationalize a dangerous action.

---

## Lost progress across context windows

**Symptom:** a task that exceeds one context window restarts and loses accumulated work.

**Fix:** checkpoint to durable artifacts (git commits, feature lists, `IMPLEMENT.md`);
**hibernate-and-wake** to resume interrupted long tasks; structured handoff where an
initializer sets up the environment once and later sessions make gated incremental progress.
See [`07-lifecycle-and-artifacts.md`](07-lifecycle-and-artifacts.md).

---

## Tool-combination risk (the lethal trifecta)

**Symptom:** individually safe tools combine into a harmful action. Single-tool safety analysis
misses it.

**Fix:** watch the **lethal trifecta** — private-data access + untrusted-content exposure +
external communication present together. Use intent-level permission enforcement (map calls to
an intent taxonomy like `filesystem_delete`, `network_outbound`) rather than command-name
allow-lists; the same binary is benign or destructive depending on arguments. Pre-action
authorization (evaluate before execution, sign an audit record) closes the window
(OWASP LLM06; `nah`; OAP, arXiv:2603.20953).

---

## Interface / persistence mismatch

**Symptom:** ~80% missing-variable errors, or ~3.5× token overhead from redundant recompute.

**Fix:** match runtime persistence semantics to the model's training-time expectations
([`04-environment-engineering.md`](04-environment-engineering.md)). Many "reasoning failures"
are actually interface mismatches (`Life-Harness`).

---

## Multi-agent handoff failures

**Symptom:** multi-agent workflows fail in ways single agents don't — dropped context,
mis-parsed handoffs, cascading errors.

**Fix:** treat every handoff as a distributed-systems boundary — **typed schemas, constrained
action schemas, explicit boundary validation** (GitHub, *Multi-Agent Workflows Often Fail*).
Subagent context isolation also cuts cross-domain token bloat (67% fewer tokens than skills in
multi-domain scenarios).

---

## Quick diagnostic table

| Symptom | Likely failure | First fix |
|---|---|---|
| Output drifted from the ask | Semantic drift | Re-anchor to persisted spec each iteration |
| Quality fades as run gets long | Context rot | Proactive compaction; pointer navigation |
| Bad work gets approved | Self-praise loop | Isolate the critic |
| Never finishes / thrashes | Runaway iteration | Stopping rules + loop detection |
| Humans rubber-stamp everything | Approval fatigue | Gate only irreversible actions |
| Restart loses work | Lost progress | Checkpoint artifacts; hibernate-and-wake |
| Safe tools, harmful result | Lethal trifecta | Intent-level pre-action authorization |
| Missing vars / recompute waste | Persistence mismatch | Match runtime semantics |
| Multi-agent cascade | Handoff failure | Typed schemas + boundary validation |

Sources: see [`../references.md`](../references.md) §Failure Modes, §Permissions, §Context.
