# Loop & Harness Kit

**A point-your-LLM-at-it reference for loop, harness, and environment engineering.**

🌐 **Website:** [satoshigreek.github.io/loop-harness-kit](https://satoshigreek.github.io/loop-harness-kit/) — a UML diagram for every research paper behind the kit.

This repo exists for one purpose: you give an LLM agent (Claude Code, Codex, Cursor, etc.)
a link to this repository — or drop it into the agent's context — and its performance on
real, multi-step work goes up. It does that by encoding the things a model *can't* reliably
do on its own: choosing the right feedback loop, structuring the harness around itself,
managing context, and recovering from the failure modes that quietly wreck long-horizon runs
(semantic drift chief among them).

It is built on top of a hand-authored visual reference of feedback loops
([`reference/ai-loops-reference.html`](reference/ai-loops-reference.html)) and folds in the
2026 wave of research and production write-ups on **harness engineering** and **environment
engineering**.

---

## How to use it

**Point your agent here.** The single most useful file for an LLM is
[`AGENTS.md`](AGENTS.md) — a compact operating manual. Tell your agent:

> Read `AGENTS.md` and the `docs/` folder in this repo, then follow its loop-selection
> procedure and failure-mode checklist for the rest of this session.

**For humans:** open [`reference/ai-loops-reference.html`](reference/ai-loops-reference.html)
in a browser for the visual catalog (rendered UML diagrams per loop), then read the `docs/`
for the engineering layer underneath it.

---

## What's inside

| Path | What it is |
|------|------------|
| [`AGENTS.md`](AGENTS.md) | The LLM-facing operating manual. Point your agent at this first. |
| [`docs/00-foundations.md`](docs/00-foundations.md) | The three layers: loop, harness, environment. Definitions and why they matter. |
| [`docs/01-the-agent-loop.md`](docs/01-the-agent-loop.md) | The observe→plan→act→verify cycle and its harness components. |
| [`docs/02-harness-engineering.md`](docs/02-harness-engineering.md) | The four necessary elements of a harness; design primitives by problem. |
| [`docs/03-context-engineering.md`](docs/03-context-engineering.md) | Context as a finite, curated resource. Compaction, retrieval-as-tool, navigation. |
| [`docs/04-environment-engineering.md`](docs/04-environment-engineering.md) | Engineering the *environment* the agent acts in (EurekAgent and friends). |
| [`docs/05-loop-patterns.md`](docs/05-loop-patterns.md) | The loop catalog (L1/L2/L3) and the selection procedure, distilled to text. |
| [`docs/06-failure-modes.md`](docs/06-failure-modes.md) | Semantic drift, context rot, approval fatigue, and how the harness prevents each. |
| [`docs/07-lifecycle-and-artifacts.md`](docs/07-lifecycle-and-artifacts.md) | Long-horizon lifecycle: Plan.md/Implement.md, hibernate-and-wake, cross-context handoff. |
| [`docs/08-self-improvement-loop.md`](docs/08-self-improvement-loop.md) | The traces→evals→handoff flywheel that keeps an agent system (and this repo) improving. |
| [`SELF-IMPROVEMENT.md`](SELF-IMPROVEMENT.md) | The weekly maintenance runbook this repo runs on itself. |
| [`CHANGELOG.md`](CHANGELOG.md) | Dated, source-cited record of every change. |
| [`templates/`](templates/) | Copy-paste harness artifacts: `PLAN.md`, `IMPLEMENT.md`, `loop-spec.md`. |
| [`references.md`](references.md) | Every primary source, with links. |
| [`reference/ai-loops-reference.html`](reference/ai-loops-reference.html) | The original visual loop reference (open in a browser). |

---

## The one-paragraph thesis

> The model is the engine; the harness is the car. Most of the variance in agent performance
> on real tasks comes not from the model but from the scaffolding around it — what context it
> sees, what tools it has, how its loop terminates, and how it recovers. Harness-only changes
> have been shown to move agents 20+ ranking positions and swing benchmarks by 5+ points with
> **no model swap**. This repo is a curated, agent-readable encoding of those levers.

See [`references.md`](references.md) for the evidence behind every claim.

## This repo improves itself

It's maintained by its own flywheel. A weekly cycle ([`SELF-IMPROVEMENT.md`](SELF-IMPROVEMENT.md))
scans authoritative lab sources (Anthropic, OpenAI, Google, Microsoft, Meta) and new research,
gates candidates through freshness/authority evals, and opens a PR with ranked, source-cited
improvements. History lives in [`CHANGELOG.md`](CHANGELOG.md).

## License

[CC0 1.0](LICENSE) — public domain. Use it however you like.
