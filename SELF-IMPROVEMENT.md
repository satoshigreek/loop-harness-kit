---
last_updated: 2026-06-24
owner: satoshigreek
scope: maintenance protocol for loop-harness-kit
reviewed_by: Claude (Opus 4.8)
cadence: weekly
---

# SELF-IMPROVEMENT.md — Weekly Maintenance Protocol

> This is the runbook an agent (or human) executes to keep this repo authoritative and fresh.
> It is the repo's own [self-improvement flywheel](docs/08-self-improvement-loop.md) applied to
> a knowledge base. **The agent dogfoods this kit's methodology while improving it.**

## When
**Weekly.** A scheduled cloud agent runs this every Monday ~08:00. See
[Automation](#automation) below. A human can also run it on demand.

## Operating rules (from this kit — follow them)
- **Ground every change in a citable, authoritative source.** No source → no claim.
  Authority order: first-party lab (Anthropic, OpenAI, Google, Microsoft, Meta) > peer
  reviewed / arXiv > reputable practitioner blog. (See `AGENTS.md` §1.)
- **Re-anchor to intent:** the repo's purpose is to *measurably improve an LLM's performance on
  real tasks* by encoding loop/harness/environment levers. Reject additions that don't serve
  that. (Defeats semantic drift — `docs/06`.)
- **Cap the change set.** Max ~5 substantive changes per cycle; quality over volume.
- **Isolated critic:** open changes as a PR, not a direct push to `main`. A human (or a
  fresh-context review agent) approves. (Gate before irreversible action — `docs/06`.)

## The loop (run top to bottom)

### 1. Scan (the "traces": state of the field)
Search each source for material published since `last_updated`:
- Anthropic — <https://www.anthropic.com/engineering> and `/research`
- OpenAI — <https://openai.com/index/> and <https://developers.openai.com/blog>
- Google — <https://developers.googleblog.com> (ADK, A2A, agents)
- Microsoft — Azure/AI agent engineering blogs
- Meta — <https://engineering.fb.com>
- arXiv — `cs.AI` / `cs.SE` for "agent harness", "context engineering", "agent loop",
  "environment engineering"
- Registry — <https://github.com/ai-boost/awesome-harness-engineering> (diff its recent commits)

### 2. Diff (the "feedback")
For each candidate, classify against current docs:
- **NEW** — a primitive/pattern/result not yet covered.
- **STALE** — a doc claim now superseded (newer model, better number, retracted advice).
- **CONTRADICTED** — a claim a new authoritative source disputes. Flag loudly.
- **DEAD LINK** — a reference that no longer resolves.

### 3. Evals (gate the change before proposing it)
A proposed change must pass all of these or it's dropped:
- [ ] **Authority** — backed by a source at or above the authority bar above.
- [ ] **Freshness** — not older than a source it would replace.
- [ ] **Non-regression** — doesn't break an existing cited claim or remove a still-valid one.
- [ ] **Link integrity** — every new link resolves; no internal cross-link is broken.
- [ ] **On-purpose** — serves the repo's intent (step 0 re-anchor).

### 4. Optimize (rank by impact)
Priority order:
1. New primitive **with a quantified result** (e.g. "p95 TTFT −90%").
2. Correction of a stale/contradicted claim.
3. New pattern without numbers.
4. Restatement / clarity improvement.
5. Link fixes.

### 5. Apply + handoff
- Make the edits on a branch `weekly/<YYYY-MM-DD>`.
- Add every new source to `references.md` under the right section.
- Append a dated entry to [`CHANGELOG.md`](CHANGELOG.md) listing each change + its source.
- Bump `last_updated` in this file's front matter.
- Open a PR titled `Weekly refresh <YYYY-MM-DD>` whose body is the ranked change set with
  evidence links (the "handoff").

### 6. Stop
- If the scan surfaces nothing new that passes the evals, **make no changes** — append a
  one-line "no changes this week" note to `CHANGELOG.md` and close. (Don't loop for its own
  sake — `AGENTS.md` §1.)

## Automation
A scheduled cloud agent (a Claude Code *routine*) runs this protocol weekly. To create or edit
it, use the `/schedule` command in Claude Code, or recreate it with this prompt:

> **Schedule:** every Monday at 08:00.
> **Prompt:** "Execute the SELF-IMPROVEMENT.md protocol in the repo
> github.com/satoshigreek/loop-harness-kit: scan the authoritative lab sources for material
> published in the last week, diff it against the current docs, gate candidates through the
> evals, apply up to 5 ranked changes on a `weekly/<date>` branch, update references.md and
> CHANGELOG.md, and open a PR. Make no changes if nothing passes the evals."

The agent should clone the repo, follow this file exactly, and open a PR for human review
(reviewed-loop trust level). Promote to deeper automation only once the eval gate has proven
itself over several cycles.
