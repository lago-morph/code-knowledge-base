# AI for Software Development: Reading List

A curated, living list of resources to help anyone in the organization get up to speed on AI-assisted software development. This list will grow. If you find something worth adding, please contribute.

## How to use this list

Each entry is tagged with one or more audiences so you can self-select:

- **[Curious]** — for developers new to AI tooling, looking for "what does this actually look like in practice?"
- **[Skeptic]** — for experienced developers wanting honest, grounded analysis (including limitations and failures, not just success stories)
- **[Manager]** — short, executive-readable, often with named-company context

Skim the entries tagged for your audience; ignore the rest. Don't try to read everything.

---

## Concrete "one team did one thing" stories

These are the most useful entries if you want to *imagine yourself doing this*. Each is a real team's writeup of a specific application of AI to a specific problem.

### Altruist Engineering — "API automation using AI agents"
**Tags:** [Curious] [Skeptic]
**URL:** https://altruist.com/engineering-blog/api-automation-using-ai-agents/

A real engineering team's writeup of an internal "API Agent" that takes natural-language test specs from TestRail and generates executable test code, request/response models, service methods, and database validation queries. Closest published reference architecture I've found for AI-assisted API testing in an enterprise setting.

*Read this if:* you want to imagine what an internal AI tool actually looks like in production, especially for end-to-end API testing.

### mabl — "How We Built a System for AI Agents to Ship Real Code Across 75+ Repos"
**Tags:** [Curious] [Skeptic]
**URL:** https://www.mabl.com/blog/how-we-built-a-system-for-ai-agents-to-ship-real-code-across-75-repos

Honest writeup of a multi-repo dogfooding effort. Explicitly addresses cross-repo coordination problems (which repos depend on which, merge order, dependency graph maintenance) that most "AI coding" content sidesteps.

*Read this if:* you want the most directly relevant published account of doing AI-assisted development in a multi-repo environment like ours.

### Bishoy Labib — "Setting Up AI Coding Assistants for Large Multi-Repo Solutions"
**Tags:** [Curious]
**URL:** https://www.bishoylabib.com/posts/ai-coding-assistants-multi-repo-solutions

Practical, hands-on walkthrough of configuring Cursor / Claude Code / Copilot across multiple repos in a microservices architecture. Less ambitious than the mabl piece but more tactical.

*Read this if:* you want to play with a multi-repo setup yourself and need a starting point.

### Kenny Th. — `jira-ticket-estimator` (Claude Code skill)
**Tags:** [Curious]
**URL:** https://github.com/kennyth01/jira-ticket-estimator

A working open-source Claude Code skill that does codebase reconnaissance to estimate Jira tickets — classifying task type, scoring complexity on five factors (scope, technical complexity, testing, risk, dependencies), and producing an estimate.

*Read this if:* you want a concrete example of what "give an AI deep code context for a specific task" looks like as actual code. You can imagine extending or adapting this.

---

## Architectural framing and honest analysis

These help you understand *why* AI on large codebases is hard, what approaches have emerged, and what the real tradeoffs are.

### Kilo Code — "AI Coding Assistants for Large Codebases: Architecture, Evaluation, and Best Practices (2026)"
**Tags:** [Skeptic]
**URL:** https://blog.kilo.ai/p/ai-coding-assistants-for-large-codebases

Vendor-neutral framing of the "AI for large codebases" problem space: hybrid indexing (AST + vector), agentic loops, MCP as standard for context retrieval. The clearest architectural overview I've found of why naive "stuff more files in the prompt" approaches don't work and what tends to work instead.

*Read this if:* you want one architectural mental model to anchor everything else you read.

### Coder — "Inside AI Adoption: Lessons from Enterprise Software Development Teams"
**Tags:** [Skeptic] [Manager]
**URL:** https://coder.com/blog/inside-ai-adoption-lessons-from-enterprise-software-development-teams

Lessons from real enterprise rollouts. Notably honest about failure modes: top-down rollouts disappointing, bottom-up adoption pulling tools in, security/governance being the actual bottleneck more than AI maturity.

*Read this if:* you're a manager evaluating this, or a skeptic wanting to know what actually goes wrong (this isn't selling you anything).

### Diffblue — "Diffblue Cover vs Claude, Copilot & Qodo: 2025 Benchmark Study"
**Tags:** [Skeptic]
**URL:** https://www.diffblue.com/resources/diffblue-cover-vs-ai-coding-assistants-benchmark-2025/

Read skeptically — this is a vendor's own benchmark, designed to make their product look good. *But* the framing of "autonomous agent vs LLM-assisted" is genuinely useful, and the discussion of determinism vs LLM-based test generation is one most marketing posts skip.

*Read this if:* you want to think clearly about what's different between specialized testing tools and general AI assistants. Read it for the framing, ignore the headline numbers.

### Panto AI — "CodeRabbit vs Greptile: AI Code Review Tools Compared"
**Tags:** [Skeptic]
**URL:** https://medium.com/@pantoai/coderabbit-vs-greptile-ai-code-review-tools-compared-8b535666f708

The signal-to-noise tradeoff in AI code review, with benchmark numbers. Greptile catches more bugs (~82%) but produces more false positives; CodeRabbit catches fewer (~44%) with fewer false positives.

*Read this if:* you're forming opinions about which AI code review tool is best. There is no single answer; this article makes that clear.

### Augment Code — "Monorepo vs Multi-Repo AI: Architecture-based AI Tool Selection"
**Tags:** [Skeptic] [Manager]
**URL:** https://www.augmentcode.com/tools/monorepo-vs-multi-repo-ai-architecture-based-ai-tool-selection

Vendor-flavored but the high-level architectural framing of why multi-repo AI is genuinely harder than monorepo AI is useful for anyone thinking about why this isn't a "just buy a tool" problem.

*Read this if:* you're a manager or skeptic wondering why we can't just adopt a vendor solution and be done.

---

## For team leads and managers specifically

Short, executive-readable pieces that don't require a deep technical background.

### Coder — "Inside AI Adoption: Lessons from Enterprise Software Development Teams"
*(See above under "Architectural framing and honest analysis.")*

The most useful management-perspective read. Short, named companies, honest about what worked and didn't.

### Figr — "AI Tools That Auto-Generate Jira Tickets from Ideas"
**Tags:** [Manager]
**URL:** https://figr.design/blog/ai-tools-that-auto-generate-jira-tickets-from-ideas

Useful frame: "tickets as task descriptions vs tickets as executable specifications." The argument is that AI ticket generation that just speeds up typing misses the point — the value is in capturing problem context, rationale, and constraints.

*Read this if:* you're thinking about AI in planning workflows and want a non-obvious framing of what good looks like.

---

## Foundational concepts

Short reference material on the underlying technology if you want to be literate in the building blocks.

### Anthropic — Model Context Protocol (MCP) overview
**Tags:** [Curious] [Skeptic]
**URL:** https://modelcontextprotocol.io/

MCP is the emerging standard for how AI agents access tools and data sources. Worth being literate in even if we don't build on it ourselves yet.

*Read this if:* you want to understand the architectural building blocks that most of the recent enterprise AI tooling assumes.

### Anthropic — Claude Code documentation
**Tags:** [Curious]
**URL:** https://docs.claude.com/en/docs/claude-code/overview

Reference material for Claude Code, one of the agent options likely to come up in pilots.

*Read this if:* you've never used a CLI-based AI coding agent and want to skim what they look like.

---

## Contributing to this list

If you read something worth adding, append it with audience tags and a short annotation explaining *why* it's worth reading. Honest "I disagreed with this but it made me think" entries are welcome.

A few guidelines for additions:

- Annotate, don't just link. The annotations are more valuable than the list.
- Aim for honest writeups including limitations and failures, not just success stories.
- For each entry, consider who it's *not* for as well as who it is for.
- If something gets stale or obsolete, remove it or mark it as historical.

---

*Maintained by the DevOps team. Living document — expect it to grow.*
