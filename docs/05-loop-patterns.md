# 05 — Loop Patterns Catalog

The text companion to [`../reference/ai-loops-reference.html`](../reference/ai-loops-reference.html)
(open it in a browser for rendered UML per pattern). Each pattern is tagged by **tier**:

- **L1** — plain chat, single actor.
- **L2** — light tooling, multi-sampling, or a rubric/critic.
- **L3** — real tools, agents, or live environment feedback.

> First, run the selection procedure ([§ below](#selection-procedure)). Don't add a loop
> unless one good pass falls short and you have a grounded feedback signal.

## Foundational techniques

### Self-Consistency — `L2` · activity, fork/join
Sample multiple independent reasoning paths, take the **majority** answer. For math, logic,
any task with one correct answer.
> *Solve this problem 5 independent times, each with fresh step-by-step reasoning. Report the
> answer that appears most often and your confidence.*

Why: many correct paths converge on one answer; majority voting filters one-off errors
(+17.9% GSM8K, Wang et al. 2022). Note: parallel sampling + aggregation, **not** iteration.

### Tree of Thoughts — `L3` · activity, search
Generate candidate next steps, self-evaluate each, expand the best, **backtrack** on dead ends.
For planning, puzzles, creative search.
> *Generate 3 distinct candidate next steps. Rate each 1–10. Expand only the top 2. Repeat
> until solved; backtrack if a branch scores low.*

Why: deliberate lookahead/backtracking over coherent "thoughts" (Game-of-24: 4% → 74%, Yao et
al. 2023).

### Least-to-Most Decomposition — `L2` · sequential loop
Decompose into ordered subproblems, solve in sequence **feeding earlier answers forward**. For
compositional, multi-step problems.
> *Stage 1: break this into ordered sub-questions. Stage 2: answer each in order, using earlier
> answers as inputs. Finish with the final answer.*

Why: separates decomposition from solving, cutting error propagation (99.7% vs 16.2% CoT on
SCAN length split, Zhou et al. 2022).

### ReAct (Reason + Act) — `L3` · sequence loop
Interleave **Thought / Action / Observation**. Whenever the model needs live information or to
execute actions. The foundational agent-loop pattern (arXiv:2210.03629).
> *Work in a loop of Thought / Action / Observation. Thought: reason about what you need next.
> Action: call a tool. Observation: read the result. Repeat until you can answer.*

### Reflexion (Verbal RL) — `L3` · state machine
After a failed attempt, write a **verbal self-reflection** and retry with it in context. For
multi-attempt tasks with a success signal.
> *Attempt the task. If it fails the check, write a short reflection on why, then retry using
> that reflection. Stop on success or after N attempts.*

Why: converts a failure signal into a textual gradient the next attempt can use.

## Generator–critic family

### Audience-Critic Refinement — `L1` · sequence loop
Draft, then critique from a **specific audience's** perspective, then revise. For
communication where fit-to-reader is the bar.

### Draft → Rubric Critique → Revise (Self-Refine) — `L1`/`L2` · activity loop
Write an explicit rubric, score the draft against it, revise. The default when quality is
subjective but articulable. **Isolate the critic** for real signal.

### Generate-then-Review Subagent — `L3` · sequence loop
A **separate** agent (fresh context) reviews the generator's output. Defeats same-context
self-praise — the single highest-value upgrade to any critique loop.

### Multi-Persona Debate → Synthesis — `L2`/`L3` · parallel + loop
Multiple personas argue distinct positions; a synthesizer reconciles. For contested judgment
calls where one viewpoint is biased.

## Optimization & search

### OPRO / DSPy Optimization Loop — `L3` · optimize loop
Treat the prompt (or program) as a parameter and optimize it against a **metric** over a
dataset. For systematic prompt/program tuning, not one-off tasks.

### Assumption-Audit + Recompute — `L2` · audit loop
Surface hidden assumptions, audit each, recompute affected results. For financial models,
analyses, anywhere a wrong assumption silently corrupts the output.

## Agentic / tool loops

### Red-Green-Refactor TDD Loop — `L3` · state machine
Write a failing test (red) → make it pass (green) → refactor. The gold standard for
tool-checkable code: the test **is** the grounded signal.

### ReAct with Code Execution — `L3` · sequence loop
ReAct where the "Action" is running code, and the interpreter's output is the observation.
Honor [persistence semantics](01-the-agent-loop.md#persistence-semantics-a-subtle-but-expensive-detail).

### Eval-Driven Development — `L3` · flywheel
Build an eval set, run the agent against it, fix the worst failures, repeat. The flywheel that
turns "it seems better" into a measured curve.

### Orchestrator-Worker + Citation Pass — `L3` · sequence, parallel
An orchestrator fans **independent** subtasks to parallel workers; a citation pass grounds the
synthesis. For broad research. Costly (~15× tokens) — reserve for high-value breadth, cap fan-out.

## Decision & reasoning

### Scoring Matrix + Red-Team + Premortem — `L2` · activity loop
Score options on weighted criteria, red-team the leader, run a premortem ("assume it failed —
why?"). For choosing among discrete options.

### Diverge → Criteria Filter → Refine — `L1` · fork + loop
Generate broadly, filter against explicit criteria, refine survivors. For creative work needing
both breadth and a quality bar.

### Explain → Probe Gaps → Refine (Feynman) — `L1` · activity loop
Explain a concept simply, probe where the explanation breaks, refine. For learning and
knowledge work.

## Production harness loops

### Claude Code: Research → Plan → Execute → Review → Ship — `L3` · human gate
The full coding lifecycle with a **human gate** at plan approval and before irreversible
actions. See [`07-lifecycle-and-artifacts.md`](07-lifecycle-and-artifacts.md).

### Cursor: Plan Mode + Agent Loop + Rules — `L3` · gate + loop
Plan-mode gate, then an agent loop bounded by repo **rules** files (the persistent-rules
pattern from [`03-context-engineering.md`](03-context-engineering.md)).

---

## Selection procedure

1. **Need a loop at all?** One good pass clears the bar → ship.
2. **What grounds the feedback?**
   - tests / code / tools → **ReAct · Red-Green-Refactor · eval-driven**
   - an explicit rubric → **Draft → Rubric → Revise**
   - an outside critic/persona → **Generator–Critic · Audience-Critic**
   - a metric to optimize → **OPRO · DSPy**
   - several agents/viewpoints → go to 3
3. **Shape of the work?**
   - independent subtasks, need breadth → **Orchestrator-Worker (parallel)**
   - contested judgment → **Multi-Persona Debate**
   - pick among options → **Scoring Matrix + Red-Team + Premortem**

### Pick the UML view from the loop's structure

| Structure | Diagram | Loop & stop shown by |
|---|---|---|
| Single actor, iterative refinement | **Activity** | merge node + decision with `[guard]`; back-edge to merge |
| Two+ roles exchanging messages | **Sequence** | `loop [guard]` / `alt` / `opt` fragment; exit on guard |
| Discrete attempts or modes | **State machine** | guarded transitions; completion transition to final state |
| Independent parallel sub-work | **Activity fork/join** or **Sequence `par`** | fork bar → join bar; no back-edge if one-shot |

### Cross-cutting rules
- **Ground the critique** — no tests/tools/rubric/data/critic ⇒ you're polishing confidence.
- **Isolate the critic** — fresh context or a different model.
- **Cap iterations (3–4)**; stop the moment evals pass.
- **Gate irreversible actions** behind a human checkpoint.
- **Re-anchor to intent** each iteration (defeats semantic drift).

Sources: per-pattern citations are inline; foundational papers in
[`../references.md`](../references.md).
