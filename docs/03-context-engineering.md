# 03 — Context Engineering

Context is the single most contested resource in an agent harness. Everything the model knows
in a turn — system prompt, tools, MCP definitions, retrieved data, message history — competes
for a finite window. Context engineering reframes the design question from *"what should the
prompt say?"* to **"what configuration of context produces the behavior I want?"** (Anthropic,
*Effective Context Engineering for AI Agents*).

## Context rot is real and silent

As the window fills, quality degrades — even below the hard limit. LangChain names it
**"context rot."** The insidious part: there's no error, just a slow decline in coherence and
instruction-following. You cannot wait for a failure signal; you have to manage context
proactively.

## The compaction discipline

**Compact proactively, between subtasks — not reactively at the limit.** Reactive-at-limit
compaction interrupts the agent mid-subtask and corrupts in-flight reasoning state
(*Autonomous Context Compression*, LangChain; *Active Context Compression*, arXiv:2601.07190).
Anthropic's server-side compaction cut token consumption **84%** in a 100-turn web-search eval
while letting agents finish workflows that would otherwise hit the wall.

**Know what survives compaction and what doesn't** (Claude Code analysis):

| Survives | Gets lost |
|---|---|
| Current task | Initial instructions |
| Recent errors | Intermediate decisions |
| File names / paths | Style rules |

> **The key rule:** never rely on compaction to preserve critical rules. Move durable rules to
> a persistent file (`CLAUDE.md` / `AGENTS.md`) where they live in the system prompt and
> survive any compression.

Claude Code's progressive compaction has five stages — budget reduction → snip → microcompact
→ context collapse → auto-compact (*The Design Space of Today's AI Agent Systems*,
arXiv:2604.14228) — a useful mental model for how pressure escalates.

**Let the agent own compression when it can.** Shifting compression from a fixed token
threshold to an agent-triggered tool call (compress between tasks, before consuming large
inputs) keeps the compression unit *semantically coherent* — the agent decides what knowledge
is worth keeping, yielding ~22% token reduction with no accuracy loss (arXiv:2601.07190).

## Retrieval is a tool call, not a preprocessing dump

The architectural decision that matters most: **do not inject everything upfront.** Expose
retrieval as tools (keyword search, semantic search, chunk read) and let the agent pull
information incrementally as each reasoning step requires it (*A-RAG*, arXiv:2602.03442). This
lets reasoning adaptively narrow scope instead of drowning in an upfront dump.

Corollary for code: navigate by **symbol/pointer**, not by reading whole files. Symbol indexes
(`token-savior`, `codebase-memory-mcp` via tree-sitter AST, `semble`) report **77–98%** active-
token reductions by turning navigation into pointer-chasing. Domain-specific pruning rubrics
(semantic evidence + dependency support) beat generic compression on coding tasks
(arXiv:2605.15315).

## Sandbox bulky output outside the window

Tool outputs (Playwright snapshots, logs, GitHub issues, large files) are a primary source of
context pressure. Intercept and compress them *before* they enter the window:

- `context-mode` sandboxes raw tool output and retrieves only relevant fragments via BM25.
- `headroom` compresses tool outputs/logs/RAG chunks 60–95% before they hit context.
- The "think in code" paradigm: replace ten file-read tool calls with one script execution.

## Extended thinking: preserve the blocks

In extended-thinking loops, **thinking blocks must be preserved when passing tool results back**
(Claude API docs). Omitting them silently breaks multi-step reasoning. `budget_tokens` controls
reasoning depth per turn, and thinking mode can't change mid-turn — set it deliberately, and
recall the reasoning sandwich (spend the budget at plan and verify).

## Caching: the cheapest cost lever

Prompt caching is the highest-ROI harness optimization for cost: cache repeated system prompts,
tool definitions, and long documents across requests. Place `cache_control` breakpoints to
maximize reuse across multi-turn sessions (Claude API docs). It changes nothing about behavior
and meaningfully cuts cost.

## A practical context checklist

- [ ] Durable rules live in a persistent file, not the scrollback.
- [ ] Compaction triggers **between** subtasks, before the limit.
- [ ] Retrieval is incremental (tool calls), not an upfront dump.
- [ ] Code navigation is by symbol/pointer, not whole-file reads.
- [ ] Bulky tool output is compressed/sandboxed before entering context.
- [ ] Thinking blocks are preserved across tool results.
- [ ] Stable prefixes (system prompt, tool defs) are cached.

Sources: see [`../references.md`](../references.md) §Context Delivery & Compaction.
