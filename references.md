# References

Primary sources behind this repo. Grouped to match the `docs/` sections. Links were live as of
June 2026.

## Primary research papers (the four you asked to fold in)

1. **Building Effective AI Coding Agents for the Terminal: Scaffolding, Harness, Context
   Engineering, and Lessons Learned** — arXiv:2603.05344.
   <https://arxiv.org/abs/2603.05344> · [HF page](https://huggingface.co/papers/2603.05344).
   The first systematic practitioner paper on terminal-native coding-agent harness design
   (eager construction, compound multi-model architecture, defense-in-depth, schema-filtered
   planning subagents — the OpenDev lessons).
2. **EurekAgent: Agent Environment Engineering is All You Need for Autonomous Scientific
   Discovery** — arXiv:2606.13662. <https://arxiv.org/abs/2606.13662> ·
   [HF page](https://huggingface.co/papers/2606.13662). The definitive statement of
   environment engineering as the dominant lever for autonomous discovery loops.
3. **Harness Engineering for Language Agents: The Harness Layer as Control, Agency, and
   Runtime** — Preprints.org 202603.1756.
   <https://www.preprints.org/manuscript/202603.1756>. Taxonomy/formalization of the harness
   layer (control, agency, runtime); context for production "hibernate-and-wake" patterns and
   loop-constraint formalization.
4. **Awesome Harness Engineering** (registry & implementations) —
   <https://github.com/ai-boost/awesome-harness-engineering>. Curated index of harness papers,
   tools, and templates; the structural-primitive reference this repo's `docs/` draws on.

## §Foundations

- *Harness Engineering* — OpenAI. <https://openai.com/index/harness-engineering/>
- *Building Effective Agents* — Anthropic.
  <https://www.anthropic.com/research/building-effective-agents>
- *Harness Design for Long-Running Application Development* — Anthropic.
  <https://www.anthropic.com/engineering/harness-design-long-running-apps>
- *Harness Engineering* — Martin Fowler.
  <https://martinfowler.com/articles/exploring-gen-ai/harness-engineering.html>
- *The Anatomy of an Agent Harness* — LangChain.
  <https://blog.langchain.com/the-anatomy-of-an-agent-harness/>
- *Scaling Managed Agents: Decoupling the brain from the hands* (session vs. harness, stable
  interfaces, p95 TTFT −90%) — Anthropic.
  <https://www.anthropic.com/engineering/managed-agents>
- *Equipping agents for the real world with Agent Skills* — Anthropic.
  <https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills>
- *What makes a harness a harness* — arXiv:2606.10106. <https://arxiv.org/abs/2606.10106>
- *Architectural Design Decisions in AI Agent Harnesses* — arXiv:2604.18071.
  <https://arxiv.org/abs/2604.18071>
- *2026 Agentic Coding Trends Report* — Anthropic.
  <https://resources.anthropic.com/hubfs/2026%20Agentic%20Coding%20Trends%20Report.pdf>
- *Harness Engineering: How to Build Reliable AI Agents…* — deepset.
  <https://www.deepset.ai/blog/harness-engineering>

## §Agent Loop

- *ReAct: Synergizing Reasoning and Acting in Language Models* — arXiv:2210.03629.
  <https://arxiv.org/abs/2210.03629>
- *Unrolling the Codex Agent Loop* — OpenAI.
  <https://openai.com/index/unrolling-the-codex-agent-loop/>
- *LangGraph — Low Level Concepts*.
  <https://langchain-ai.github.io/langgraph/concepts/low_level/>
- *Improving Deep Agents with Harness Engineering* (rank 30 → top 5) — LangChain.
  <https://blog.langchain.com/improving-deep-agents-with-harness-engineering/>
- *How Middleware Lets You Customize Your Agent Harness* — LangChain.
  <https://blog.langchain.com/how-middleware-lets-you-customize-your-agent-harness/>
- *Hooks – Codex* — OpenAI. <https://developers.openai.com/codex/hooks>
- *Agents Learn Their Runtime: Interpreter Persistence…* — arXiv:2603.01209.
  <https://arxiv.org/abs/2603.01209>
- *Real-Time Deadlines Reveal Temporal Awareness Failures…* — arXiv:2601.13206.
  <https://arxiv.org/abs/2601.13206>
- *A Scheduler-Theoretic Framework for LLM Agent Execution* — arXiv:2604.11378.
  <https://arxiv.org/abs/2604.11378>
- *statewright* (tool-space constraints). <https://github.com/statewright/statewright>
- *The Design Space of Today's and Future AI Agent Systems* — arXiv:2604.14228.
  <https://arxiv.org/abs/2604.14228>
- *Introducing dynamic workflows in Claude Code* — Anthropic.
  <https://claude.com/blog/introducing-dynamic-workflows-in-claude-code>
- *Building more with GPT-5.1-Codex-Max* (compaction over long horizons, multi-hour agent
  loops) — OpenAI. <https://openai.com/index/gpt-5-1-codex-max/>

## §Harness Engineering / Design Primitives

- *Writing Effective Tools for Agents* — Anthropic.
  <https://www.anthropic.com/engineering/writing-effective-tools-for-agents>
- *Tool Annotations as Risk Vocabulary* (lethal trifecta) — MCP.
  <https://blog.modelcontextprotocol.io/posts/2026-03-16-tool-annotations/>
- *TopoCurate: Modeling Interaction Topology…* — arXiv:2603.01714.
  <https://arxiv.org/abs/2603.01714>
- *AutoHarness: Automatically Synthesizing a Code Harness* — arXiv:2603.03329.
  <https://arxiv.org/abs/2603.03329>
- *Plan-and-Execute Agents* — LangChain.
  <https://blog.langchain.com/plan-and-execute-agents/>
- *Plan-and-Act* — arXiv:2503.09572. <https://arxiv.org/abs/2503.09572>
- *Task-Decoupled Planning for Long-Horizon Agents* — arXiv:2601.07577.
  <https://arxiv.org/abs/2601.07577>
- *Task-Adaptive Multi-Agent Orchestration (AdaptOrch)* — arXiv:2602.16873.
  <https://arxiv.org/abs/2602.16873>
- *Multi-Agent Workflows Often Fail…* — GitHub.
  <https://github.blog/ai-and-ml/generative-ai/multi-agent-workflows-often-fail-heres-how-to-engineer-ones-that-dont/>
- *Demystifying Evals for AI Agents* — Anthropic.
  <https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents>
- *LLM Readiness Harness* — arXiv:2603.27355. <https://arxiv.org/abs/2603.27355>
- *Code Execution with MCP* (98.7% token reduction) — Anthropic.
  <https://www.anthropic.com/engineering/code-execution-with-mcp>

## §Self-Improvement

- *Build an Agent Improvement Loop with Traces, Evals, and Codex* — OpenAI cookbook.
  <https://developers.openai.com/cookbook/examples/agents_sdk/agent_improvement_loop>
- *The next evolution of the Agents SDK* — OpenAI.
  <https://openai.com/index/the-next-evolution-of-the-agents-sdk/>
- *Demystifying Evals for AI Agents* — Anthropic.
  <https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents>

## §Context Engineering

- *Effective Context Engineering for AI Agents* (compaction, structured note-taking,
  sub-agents, just-in-time & hybrid retrieval, tool-result clearing, attention budget) —
  Anthropic.
  <https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents>
- *Compaction* — Claude API docs.
  <https://platform.claude.com/docs/en/build-with-claude/compaction>
- *Autonomous Context Compression* — LangChain.
  <https://blog.langchain.com/autonomous-context-compression/>
- *Active Context Compression* — arXiv:2601.07190. <https://arxiv.org/abs/2601.07190>
- *A-RAG: Hierarchical Retrieval Interfaces* — arXiv:2602.03442.
  <https://arxiv.org/abs/2602.03442>
- *Context Pruning for Coding Agents via Multi-Rubric Latent Reasoning* — arXiv:2605.15315.
  <https://arxiv.org/abs/2605.15315>
- *Claude Code Compaction: How Context Compression Works*.
  <https://okhlopkov.com/claude-code-compaction-explained/>
- *Prompt Caching* — Claude API docs.
  <https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching>
- *Extended Thinking* — Claude API docs.
  <https://docs.anthropic.com/en/docs/build-with-claude/extended-thinking>

## §Environment Engineering

- *Context Engineering for Reliable AI Agents: Lessons from Building Azure SRE Agent* —
  Microsoft (45% → 75%).
  <https://techcommunity.microsoft.com/blog/appsonazureblog/context-engineering-lessons-from-building-azure-sre-agent/4481200/>
- *Life-Harness* (18-backbone transfer). <https://github.com/Tianshi-Xu/Life-Harness>
- *Making Agent-Friendly Pages with Content Negotiation* — Vercel.
  <https://vercel.com/blog/making-agent-friendly-pages-with-content-negotiation>
- *Harness Engineering: Structured Workflows for AI-Assisted Development* — Red Hat.
  <https://developers.redhat.com/articles/2026/04/07/harness-engineering-structured-workflows-ai-assisted-development>

## §Planning & Lifecycle

- *Run Long-Horizon Tasks with Codex* (Plan.md / Implement.md) — OpenAI.
  <https://developers.openai.com/blog/run-long-horizon-tasks-with-codex/>
- *Effective Harnesses for Long-Running Agents* (initializer → worker) — Anthropic.
  <https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents>
- *Ranking Engineer Agent (REA)* — Meta (hibernate-and-wake).
  <https://engineering.fb.com/2026/03/17/developer-tools/ranking-engineer-agent-rea-autonomous-ai-system-accelerating-meta-ads-ranking-innovation/>

## §Failure Modes / Permissions

- *Beyond Permission Prompts* — Anthropic.
  <https://www.anthropic.com/engineering/beyond-permission-prompts>
- *Claude Code Auto Mode: A Safer Way to Skip Permissions* — Anthropic.
  <https://www.anthropic.com/engineering/claude-code-auto-mode>
- *OWASP LLM06:2025 — Excessive Agency*.
  <https://genai.owasp.org/llmrisk/llm062025-excessive-agency/>
- *Open Agent Passport (OAP)* — arXiv:2603.20953. <https://arxiv.org/abs/2603.20953>
- *An Update on Recent Claude Code Quality Reports* (harness-regression postmortem) — Anthropic.
  <https://www.anthropic.com/engineering/april-23-postmortem>

## Loop-pattern foundational papers

- Self-Consistency — Wang et al. 2022. <https://arxiv.org/abs/2203.11171>
- Tree of Thoughts — Yao et al. 2023. <https://arxiv.org/abs/2305.10601>
- Least-to-Most Prompting — Zhou et al. 2022. <https://arxiv.org/abs/2205.10625>
- Reflexion — Shinn et al. 2023. <https://arxiv.org/abs/2303.11366>
- OPRO (LLMs as Optimizers) — Yang et al. 2023. <https://arxiv.org/abs/2309.03409>
- DSPy — Khattab et al. 2023. <https://arxiv.org/abs/2310.03714>

---

*The visual loop catalog ([`reference/ai-loops-reference.html`](reference/ai-loops-reference.html))
is the original hand-authored reference this kit was built on; its per-pattern citations appear
inline in the document.*
