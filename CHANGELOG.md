# Changelog

All notable changes to this kit. Each entry cites the authoritative source behind it.
Maintained via [`SELF-IMPROVEMENT.md`](SELF-IMPROVEMENT.md).

## 2026-06-24 — Weekly refresh (cycle #1)

First run of the [`SELF-IMPROVEMENT.md`](SELF-IMPROVEMENT.md) protocol. Scanned Anthropic /
OpenAI / arXiv; 4 candidates passed the eval gate (authority + freshness + non-regression +
link integrity). Every arXiv title was verified against the abstract before citing.

### Added
- **`docs/02` — Advanced tool use (GA)**: Tool Search (**−85%** tool-def tokens; Opus 4.5
  accuracy 79.5% → 88.1%), Programmatic Tool Calling (**−37%** tokens; powers Claude for
  Excel), Tool Use Examples (72% → 90%). Source: Anthropic, *Introducing advanced tool use*.
- **`docs/08` — research backing for the flywheel**: the three observability pillars
  (component / experience / decision) and "each edit is a falsifiable contract"; Terminal-Bench
  2 **69.7% → 77.0%**; *factual harness structure transfers, prose-level strategy does not*.
  Source: *Agentic Harness Engineering*, arXiv:2604.25850. Plus harness-aware measurement from
  *Harness-Bench*, arXiv:2605.27922.
- **`docs/00` — binding-constraint thesis** formalized via *Harness-Bench* (arXiv:2605.27922).
- **`docs/01` — graded verify outcomes**: Plan-Execute-Verify with accept / revise / escalate
  / rollback and deterministic sensors. Source: *Code as Agent Harness*, arXiv:2605.18747.

### Changed
- `references.md` — added the four sources under §Self-Improvement, §Harness, §Agent Loop.

## 2026-06-24 — Authoritative refresh #1

Dogfooded the kit's own research → ground → revise discipline; folded in the latest
first-party lab material.

### Added
- **`docs/08-self-improvement-loop.md`** — the traces → feedback → evals → validation →
  optimize → handoff flywheel, with a "reviewed loop vs. deeper automation" trust model, and an
  adaptation of the flywheel to a knowledge repo. Source: OpenAI, *Build an Agent Improvement
  Loop with Traces, Evals, and Codex*.
- **`SELF-IMPROVEMENT.md`** — weekly maintenance runbook (scan → diff → eval-gate → rank →
  apply → PR), plus the scheduled-agent definition.
- **`docs/03` — Anthropic's named context toolkit**: compaction, structured note-taking,
  sub-agent architectures, just-in-time retrieval, hybrid retrieval, tool-result clearing;
  the "smallest set of high-signal tokens" goal and the **attention budget** framing. Source:
  Anthropic, *Effective Context Engineering for AI Agents*.
- **`docs/02` — "Decouple the brain from the hands"**: session-as-durable-event-log vs.
  harness context transforms, hands-as-tools, `wake(sessionId)` stateless recovery, and the
  concrete expiry example (Sonnet 4.5 "context anxiety" resets becoming dead weight on Opus
  4.5). Includes the p50 TTFT −60% / p95 −90% result. Source: Anthropic, *Scaling Managed
  Agents: Decoupling the brain from the hands*.

### Changed
- `README.md` and `AGENTS.md` now reference `docs/08` and the self-improvement protocol; added
  metadata front matter (the 2026 `AGENTS.md` best practice).
- `references.md` — new **§Self-Improvement** and additions under §Context, §Harness, §Loop.

## 2026-06-24 — Initial release

- Visual loop reference (`reference/ai-loops-reference.html`), `AGENTS.md` operating manual,
  docs 00–07, templates (`PLAN.md`, `IMPLEMENT.md`, `loop-spec.md`), and `references.md`.
- Folded in arXiv:2603.05344, arXiv:2606.13662, Preprints.org 202603.1756, and the
  `ai-boost/awesome-harness-engineering` registry.
