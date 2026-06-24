# 07 — Lifecycle & Artifacts (Long-Horizon Work)

When a task exceeds a single context window — a multi-day pipeline, a large migration, a
feature spanning many sessions — the harness must carry **state across contexts**. Naïve
restarts lose accumulated progress. The solution is durable artifacts that act as harness
state, plus lifecycle patterns that resume cleanly.

## Planning artifacts as harness state

OpenAI's *Run Long-Horizon Tasks with Codex* introduced milestone-based planning artifacts;
Anthropic's *Harness Design for Long-Running Application Development* generalized them. The two
core files:

- **`PLAN.md`** — the milestone plan. Written once at the start; the **source of truth for
  intent**. This is your primary defense against semantic drift: every iteration re-anchors to
  it. Template: [`../templates/PLAN.md`](../templates/PLAN.md).
- **`IMPLEMENT.md`** — the running ledger: what's done, what's in progress, what's next, and
  open decisions. Updated every session. Template:
  [`../templates/IMPLEMENT.md`](../templates/IMPLEMENT.md).

These live in the repo, survive compaction, and are the first things a resuming agent reads.

## The Research → Plan → Execute → Review → Ship lifecycle

The production coding lifecycle (Claude Code), with explicit gates:

```
RESEARCH ──▶ PLAN ──[human gate]──▶ EXECUTE ──▶ REVIEW ──[gate]──▶ SHIP
   │           │                       │           │
 read the   write PLAN.md         work the      isolated
 codebase,  (intent + criteria)   plan, update  critic +
 gather                           IMPLEMENT.md   tests/evals
 context
```

- **Research** before planning — build context first (it's cheaper to read than to redo).
- **Plan approval is a human gate** — the cheapest place to catch a wrong direction.
- **Execute** against the plan, updating the ledger; replan only when reality diverges.
- **Review** with an isolated critic and computational checks (tests, lint, types).
- **Ship** behind a gate for anything irreversible.

## Hibernate-and-wake (resuming interrupted long tasks)

Meta's Ranking Engineer Agent runs multi-day ML pipeline automation where a single turn can
exceed model context, yet the pipeline must stay coherent across days. The pattern:
**checkpoint enough state to hibernate, then wake and resume** without losing context.

What to checkpoint:
- The current milestone and its acceptance criteria (from `PLAN.md`).
- Completed work as **durable, verifiable** units (git commits, passing test gates, a feature
  list with checkmarks) — not prose summaries that can drift.
- Open decisions and their rationale.
- The exact next action.

On wake: read `PLAN.md` + `IMPLEMENT.md` + the commit log, verify the last checkpoint's gate
still passes, then continue. Because progress is anchored in verifiable artifacts, a wake never
trusts a possibly-drifted summary.

## Structured handoff (initializer → worker)

Anthropic's *Effective Harnesses for Long-Running Agents* pattern: an **initializer agent**
sets up the environment **once** (scaffolding, dependencies, the plan), then hands off to a
**coding agent** that makes incremental progress each session. Cross-session state is carried
by feature lists, git commits, and test gates — concrete, verifiable artifacts rather than
context that evaporates on restart.

```
INITIALIZER (once)                WORKER (each session)
──────────────────                ─────────────────────
set up environment        ──▶     read PLAN.md + IMPLEMENT.md + git log
write PLAN.md                      verify last gate passes
scaffold + deps                   do next increment
first IMPLEMENT.md                 commit + update IMPLEMENT.md + gate
                          ◀──      repeat / hibernate
```

## Plan-in-code for very large fan-out

When work exceeds even a structured single-agent context — e.g. a 750k-line port — put the
**plan in executable code** rather than the model's context window. Anthropic's dynamic
workflows generate a JavaScript orchestration script that fans work out to many parallel
subagents with adversarial verification. The plan scaling beyond one context window is the
whole point.

## Cross-session memory

For knowledge that should persist across tasks (not just one project's progress), a memory
layer complements planning artifacts: tiered memory (core / archival / recall — Letta/MemGPT),
consolidation pipelines (episodes → facts → relationships → patterns — `Stash`,
`TencentDB-Agent-Memory`), or a drop-in layer (`mem0`, `Zep`). Keep durable *rules* in
`AGENTS.md`/`CLAUDE.md`; keep accumulated *facts* in the memory layer.

## Artifact checklist for any long-horizon task

- [ ] `PLAN.md` written before execution; intent + acceptance criteria explicit.
- [ ] `IMPLEMENT.md` updated every session.
- [ ] Progress checkpointed as **verifiable** units (commits, passing gates), not prose.
- [ ] A human gate before plan approval and before irreversible ship steps.
- [ ] Resume path: read artifacts → verify last gate → continue.
- [ ] Durable rules in `AGENTS.md`; accumulated facts in a memory layer.

Sources: see [`../references.md`](../references.md) §Planning & Lifecycle.
