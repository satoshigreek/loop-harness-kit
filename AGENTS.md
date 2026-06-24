# AGENTS.md — Operating Manual

> You are an LLM agent. This file is written **for you**. Read it before you start
> multi-step work, and keep it in context. It tells you how to pick a feedback loop,
> structure your own harness, and avoid the failure modes that wreck long-horizon runs.
> The longer docs in `docs/` expand each section; the cited sources live in `references.md`.

---

## 0. The prime directive

Most of your performance on real tasks is determined by the **harness** around you — the
context you see, the tools you call, how your loop terminates, how you recover — not by raw
capability. Treat the structure of your work as a first-class engineering object. When you
are struggling, the fix is usually a harness fix (better context, a tighter loop, a
verification step), not "try harder."

---

## 1. Decide whether you even need a loop

Run this before adding any iteration. A loop is only worth running if it adds a **grounded
feedback signal**.

1. **Does one good pass clear the bar?** If yes, ship it. Do not loop for its own sake.
2. **Where does the feedback come from?** Rank the signal, strongest to weakest:
   environment ground truth (tests, compiler, tool output) → retrieved evidence → an
   independent critic/judge → an explicit rubric → bare self-critique. The stronger the
   signal, the more the loop improves *correctness* rather than just polish.
3. **Verifiable or judgment?** Verifiable → lean on tools/tests. Subjective → write a rubric
   or use a critic/persona.
4. **One actor or several?** One model iterating → simple refinement loop. Multiple roles
   exchanging messages → a conversation/orchestration loop.
5. **Independent or dependent sub-work?** Independent → parallelize. Dependent → sequential.
6. **What is the cost ceiling?** Multi-agent loops can burn ~15× the tokens of a single
   pass. Reserve them for high-value breadth, and **always cap iterations (3–4)**.

> **Never** run a reasoning-only self-critique loop with no fresh signal and call it
> verification. It polishes confidence, not correctness.

---

## 2. Pick the loop (situation → pattern)

| If your situation is… | Use | Tier |
|---|---|---|
| One pass already clears the bar | **No loop** — ship | — |
| Output is code or otherwise tool-checkable | **ReAct · Red-Green-Refactor · eval-driven** | L3 |
| Subjective but you can write a rubric | **Draft → Rubric → Revise** (Self-Refine) | L1–L2 |
| You need an outside viewpoint/audience lens | **Generator–Critic · Audience-Critic** | L1–L2 |
| Tuning a prompt against a metric | **OPRO · DSPy** | L3 |
| One answer, many valid reasoning paths | **Self-Consistency** (sample N, majority vote) | L2 |
| Compositional, multi-step problem | **Least-to-Most** (decompose, feed forward) | L2 |
| Open-ended search or planning | **Tree of Thoughts** (branch, score, backtrack) | L3 |
| Multi-attempt task with a success signal | **Reflexion** (verbal RL from failure) | L3 |
| Broad research, independent subtasks | **Orchestrator–Worker** (parallel + citation pass) | L3 |
| A contested strategic call | **Multi-Persona Debate → Synthesis** | L2–L3 |
| Choosing among discrete options | **Scoring Matrix + Red-Team + Premortem** | L2 |
| Explaining or learning a concept | **Explain → Probe Gaps → Refine** (Feynman) | L1 |

Tiers: **L1** = plain chat, **L2** = light tooling/multi-sample, **L3** = real tools, agents,
or environment feedback. Full catalog with prompts and diagrams: `docs/05-loop-patterns.md`
and `reference/ai-loops-reference.html`.

---

## 3. Structure your harness (the four necessary elements)

A harness is the runtime layer around you. It is constituted by exactly four things — if any
is missing you don't have a harness, you have a tool wrapper:

1. **An agent loop** — observe → plan → act → verify, with an explicit termination condition.
2. **A tool interface** — well-named tools, typed schemas, informative error surfaces.
3. **Context management** — curate what enters the window; compact before you hit the wall.
4. **Control mechanisms** — permissions, gates, hooks, loop-detection, stopping rules.

Design heuristics:

- **Tool design is your UX.** Prefer a few well-named tools with clear errors over many
  overlapping ones. Read a tool's error as a signal to change approach, not to retry verbatim.
- **Think in code when tool calls multiply.** Replacing ten file-reads with one script, or
  wrapping multi-step tool interaction in a single code execution, can cut context by up to
  ~98%. Navigate codebases by symbol/pointer, not by reading whole files.
- **Concentrate reasoning where it pays.** Spend maximum thinking at **planning** and
  **verification**; act cheaply in between (the "reasoning sandwich").
- **Constrain the tool space per phase.** Shrinking which tools are legal in each phase of a
  workflow turns an open-ended loop into deterministic state transitions and measurably lifts
  reliability — sometimes more than a bigger model would.

---

## 4. Manage context deliberately

Context is a finite, curated resource — not a dumping ground. Context bloat ("context rot")
degrades you silently.

- **Curate, don't accumulate.** Ask "what configuration of context produces the behavior I
  want?" — then include only that.
- **Compact proactively, between subtasks** — not reactively at the limit, which corrupts
  in-flight reasoning. When you compact, preserve: current task, recent errors, file
  names/paths, and any hard rules. Expect to lose intermediate decisions and style rules —
  so put durable rules in a persistent file (e.g. `CLAUDE.md`/`AGENTS.md`), not in the
  scrollback.
- **Retrieve as a tool call, not a preprocessing dump.** Pull information incrementally as
  each reasoning step needs it, so you can adaptively narrow scope.
- **Preserve thinking blocks** across tool results in extended-thinking loops; dropping them
  silently breaks multi-step reasoning.

More: `docs/03-context-engineering.md`.

---

## 5. Engineer the environment (not just the prompt)

For autonomous, long-horizon, or discovery-style tasks, the biggest lever is often the
**environment** the agent acts in — the contract between agent and world. Make the
environment legible and the feedback fast:

- Expose state as **files the agent can read/grep/write**, rather than bespoke tools, when
  feasible — filesystem-as-context repeatedly beats large bespoke toolsets on novel tasks.
- Give the agent **fast, honest feedback**: tests, type checks, linters, runtime metrics
  wired directly into the loop. Computational feedback (a failing test) beats inferential
  feedback (an LLM judge) whenever it's available.
- Match runtime **persistence semantics** to what the model expects (does interpreter state
  persist between calls?). A mismatch causes either missing-variable errors or redundant
  recomputation.

More: `docs/04-environment-engineering.md`.

---

## 6. Failure-mode checklist (read before long runs)

| Failure mode | Symptom | Harness fix |
|---|---|---|
| **Semantic drift** | Output slowly diverges from the original intent over many turns | Re-anchor each loop to a persisted spec/goal artifact; restate acceptance criteria before each iteration; verify against the *original* requirement, not the last draft |
| **Context rot** | Quality degrades as the window fills | Proactive compaction; navigate by pointer; keep durable rules in a persistent file |
| **Self-praise loops** | Same-context "review" approves bad work | Isolate the critic — fresh context or a different model/role |
| **Runaway iteration** | Loop never converges, cost compounds | Cap iterations (3–4); stop the moment evals pass; add loop-detection |
| **Approval fatigue** | Every action gated → human rubber-stamps everything | Gate only irreversible/high-risk actions; deny-and-continue on the rest |
| **Lost progress** | A multi-context task restarts and loses work | Checkpoint to durable artifacts (commits, feature lists, `IMPLEMENT.md`); hibernate-and-wake |
| **Tool-combination risk** | Individually safe tools combine into a dangerous action | Watch the "lethal trifecta": private data + untrusted content + external send |

More: `docs/06-failure-modes.md`.

---

## 7. Cross-cutting rules (apply to every loop)

1. **Ground the critique.** If you can't point to tests, tools, a rubric, retrieved data, or
   an independent critic, assume the loop is polishing confidence, not correctness.
2. **Isolate the critic.** A fresh-context reviewer or distinct model catches far more than
   same-context self-praise.
3. **Cap iterations (3–4)** and stop the moment evals pass. More rounds rarely help; cost
   compounds.
4. **Gate irreversible actions** behind a checkpoint — before writes, sends, deploys, or
   anything you can't undo.
5. **Re-anchor to intent** at the top of every iteration to defeat semantic drift.

---

## 8. For long-horizon / multi-session work

Use durable artifacts as harness state (templates in `templates/`):

- **`PLAN.md`** — the milestone plan; written once, the source of truth for intent.
- **`IMPLEMENT.md`** — running progress: what's done, what's next, open decisions.
- **Hibernate-and-wake** — checkpoint enough state to resume a task that exceeds a single
  context window without losing coherence.
- **Structured handoff** — an initializer sets up the environment once; subsequent sessions
  make incremental progress gated by tests/commits. Naïve restarts lose accumulated progress.

Full lifecycle: `docs/07-lifecycle-and-artifacts.md`.
