# LOOP SPEC — <loop name>

> A one-page spec for a single feedback loop before you run it. Forces the discipline:
> *no loop without a grounded signal, an isolated critic, and a stopping rule.*

## 1. Do I need a loop at all?
- Does one good pass clear the bar? **<yes → ship, stop here / no → continue>**

## 2. The grounded feedback signal
- **Source:** <environment ground truth | retrieved evidence | independent critic | rubric>
- **Strength:** <strong = correctness | weak = polish>
- If the only signal is bare self-critique with no fresh input: **redesign** — it polishes
  confidence, not correctness.

## 3. Pattern
- **Chosen pattern:** <ReAct | Self-Refine | Reflexion | ToT | Orchestrator-Worker | …>
- **Tier:** <L1 | L2 | L3>
- **Why this one:** <map from situation → pattern; see docs/05>

## 4. Structure
- **Actors:** <single model | named roles>
- **UML view:** <activity | sequence | state machine | fork/join>
- **Critic isolation:** <fresh context | different model | same context ❌>

## 5. Stopping rule (required)
- **Guard:** <stop when … e.g. tests green / rubric satisfied>
- **Iteration cap:** <3–4>
- **Budget cap:** <tokens / time>
- **Loop-detection:** <break on repeated state? yes/no>

## 6. Anti-drift
- **Intent anchor:** <which artifact is re-read each iteration — e.g. PLAN.md>
- **Verify against:** <the original requirement, NOT the last draft>

## 7. Gates
- **Irreversible actions gated:** <writes / sends / deploys behind a human checkpoint>
