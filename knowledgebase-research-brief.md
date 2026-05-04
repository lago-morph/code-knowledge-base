# Research Brief: AI Coding Tools and the Shared Code Knowledge Problem

## Context

A development organization is exploring how to apply AI-assisted development to seven use cases:

1. Increasing unit test coverage
2. Application-wide refactoring (de-duplicating "paste-and-modify" code)
3. AI code reviews on PRs
4. Increasing automated end-to-end API testing (and the associated test data maintenance problem)
5. Grooming Jira tickets to assess complexity of new feature work
6. Assisted bug triage
7. General pair-programming with deep codebase context

The team's working observation is that all seven appear to require deep, persistent understanding of the codebase — conventions, architecture, dependencies, domain concepts, test fixtures, historical decisions. Building that understanding fresh per task is wasteful. The team's working hypothesis is that a shared, expert-curated, queryable representation of "the system" should sit underneath all of these AI consumers.

The team is in early discovery and wants to ground this hypothesis (or invalidate it) before committing to any architecture, vendor, or build effort. The codebase consists of roughly 20 git repositories; cross-repo coordination is part of the problem.

## Research questions

### Question 1 — State of the art in AI coding tools that depend on persistent codebase understanding

Map the current landscape (commercial, open source, academic). For each significant offering or approach, capture:

- What underlying representation it builds (vector index, AST/code graph, knowledge graph, fine-tuned model, hybrid)
- How that representation is maintained as code changes
- How it handles cross-repo or multi-service codebases
- Published evidence of effectiveness, including the vendor's own data and any independent evaluations
- Honest accounts of limitations, failures, and operational costs

Cover at minimum: commercial offerings (e.g., Augment Code, Sourcegraph Cody, Greptile, CodeRabbit, Diffblue, Qodo, Tabnine Enterprise, Bito, Cursor, Windsurf, GitHub Copilot), open source projects (e.g., Aider, Continue.dev, PR-Agent/Qodo Merge, Graph-Code, Graphify, OpenCode, Cline), and any academic systems with significant practitioner attention. Identify gaps and emerging entrants.

Temporal focus: developments in the past 2-3 years, with attention to the most recent 12 months for fast-moving subareas.

### Question 2 — Pre-LLM research that informs this problem

This is not a wholly new problem. Software engineering research has been working on code understanding, navigation, and generation for decades. Surface and synthesize the prior art that informs how to build a shared code knowledge layer:

- Program comprehension and concept location research
- Architecture recovery and reflexion model literature
- Code search systems (Sourcegraph's indexing approach, Google Kythe, GitHub Semantic, OpenGrok, etc.)
- Code property graphs (Joern et al.) and AST-based static analysis
- Mining Software Repositories (MSR conference body of work)
- "Naturalness of software" / Big Code research line (Hindle, Allamanis, Sutton, et al.)
- Software metrics and the Halstead/McCabe lineage as it applies to complexity estimation
- Documentation generation lineage (Doxygen, Javadoc, and more recent automated approaches)
- Type inference and program analysis as a substrate for understanding
- Dependency graph and reverse engineering tooling (Eclipse JDT, IntelliJ PSI, language servers, tree-sitter, ctags)

For each strand: what is the durable insight that should inform design decisions today? What did pre-LLM tooling get right that current AI tooling appears to ignore or reinvent badly?

### Question 3 — Critical analysis of the seven use cases

The agent should push back rather than accept the framing. For each of the seven use cases:

- **What structured data is *actually* required?** Be specific. A unit test generator may need only the target function's call graph, type information, and existing test patterns — not a full architectural model. A PR reviewer flagging style violations may need very little persistent context. Refactoring across the codebase is the most data-hungry. Pair-programming sits in the middle.
- **Is the working hypothesis ("they all need the same shared layer") actually correct?** Or do the requirements diverge enough that a single layer is the wrong abstraction?
- **Where is the line between "AI needs persistent context" and "AI needs task-time hydration of relevant snippets"?** This is the central architectural question.
- **Which use cases are over-specified by the team's framing** — i.e., would work fine with simpler retrieval and don't justify a heavy shared layer?
- **Which are under-specified** — i.e., would need significantly more than a code knowledge layer to be solved well? (Test data management is an obvious candidate; surface others.)

Deliverable: a critical reframing of the seven use cases by data requirements, not a defense of the team's framing.

### Question 4 — Other use cases that would benefit from the same data archive

The seven use cases came from internal discussion and may miss important categories. Identify use cases widely discussed in industry or research where a shared code knowledge layer demonstrably improves productivity or code quality. Candidates to investigate (not exhaustive):

- Onboarding new engineers / generated codebase tours
- Architectural decision record (ADR) maintenance and consistency checking
- Dead code and unused dependency detection at scale
- Security vulnerability triage and exploitability assessment
- Migration assistance (framework upgrades, language version migrations)
- API contract change impact analysis
- Documentation drift detection
- Incident response and on-call assistance
- License and supply chain compliance
- Production observability ↔ source code mapping

For each surfaced use case: what is actually being done in industry, with evidence, not speculation.

## Source guidance

This is a fast-moving area where peer-reviewed publication lags practice by months or years. Bias the research accordingly:

- **Lean into for current state:** engineering blogs (company tech blogs and individual practitioner blogs), conference talks (especially recordings on YouTube — AI Engineer Summit, KubeCon, QCon, Strange Loop, etc.), podcast transcripts (Latent Space, Practical AI, Pragmatic Engineer, MLST), in-depth Twitter/X threads from credible practitioners, HackerNews discussions on relevant submissions, Lobste.rs, GitHub discussions and issues on relevant projects, vendor engineering blogs (treated as primary sources for what their tools do, but skeptically for performance claims).
- **Use academic sources for:** the pre-LLM precedent work in Question 2, foundational concepts (program analysis, code search, mining software repositories), and any peer-reviewed evaluations of current AI coding tools.
- **Treat vendor benchmarks skeptically.** When a vendor publishes a benchmark, also seek independent evaluations or critical commentary. Explicitly note when only the vendor's own numbers are available.
- **Surface dissent.** Where credible practitioners disagree with the prevailing narrative, capture both sides. The honest skeptics are often more useful than the enthusiasts.
- **Identify named practitioners** worth following on this topic, so the team can continue tracking the space after this report.

## Output format

Deliver a structured report:

1. **Executive summary** (~1 page) — the answers in compressed form, including an honest take on the team's working hypothesis.
2. **Landscape map** for Question 1 — table or matrix of vendors and open source projects against: representation type, maintenance approach, multi-repo handling, evidence of effectiveness, known limitations.
3. **Pre-LLM precedent synthesis** for Question 2 — what's durable, what's still relevant, where current AI tooling is reinventing prior work badly.
4. **Critical reframing of the seven use cases** for Question 3 — explicit data requirements per use case, identification of where the "shared layer" hypothesis holds and where it doesn't, and which use cases the team should reconsider.
5. **Additional use cases** for Question 4 — with evidence of where they're already being done well.
6. **Annotated source list** — every source rated for credibility (vendor / independent practitioner / academic / community discussion) with a one-line note on what it contributes. Aim for breadth.

## What to push back on

The brief itself may have framing problems. Push back where appropriate. In particular:

- The "shared layer" hypothesis may be wrong or over-general; if so, say so and propose alternatives.
- Some of the seven use cases may not be good candidates for AI assistance regardless of data architecture.
- The team's plan to "learn what to build through a small pilot" may itself be flawed; surface counterarguments and what successful organizations did differently.
- The 20-repo / cross-repo framing may matter more or less than the team assumes.
- The split between "general-purpose AI tools first, specialized vendor tools later" may be the wrong sequence.

A research report that confirms everything the team already thinks is much less valuable than one that surfaces what they're missing.
