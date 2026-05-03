# Brainstorming Notes: AI for Software Development

**Status: Working notes. Not polished. Captures the discussion that informed the initial position paper. Should be updated as we learn. Honest about what we don't know.**

**Last updated:** [add date when committed]

---

## 1. Why this document exists

This is a place to keep track of:

- What we discussed early on
- What we decided and why
- What we tried and how it went
- What we don't know yet
- What's still undecided

Maintain it. Add to it. Don't worry about polish — the point is continuity. When this grows beyond the initial pilot, we won't have to rebuild context from scratch.

---

## 2. Initial problem framing

The seven candidate use cases on the table:

1. Increase unit test coverage
2. Application-wide refactoring (de-duplicating "paste-and-modify" code)
3. AI code reviews on PRs
4. Increasing automated end-to-end API testing — with test data maintenance as a major sub-challenge (keeping test data and tests up to date as code changes)
5. Grooming Jira tickets to assess complexity / difficulty of new feature work
6. Assisted bug triage
7. General pair-programming with deep codebase context — agent has much deeper understanding than from analyzing a single file or repo

**Common observation:** all seven require deep understanding of the codebase, conventions, architecture, domain knowledge, test data, Jira state, etc. Doing this analysis fresh per task is wasteful and produces inconsistent results.

**Initial intuition:** there's a shared, central, expert-curated representation that should sit underneath all of this — built up front, reviewed by architectural and domain experts, and maintained by monitoring merges into the codebase. AI consumers (test generation, PR review, refactoring, etc.) all query the same layer.

Architecturally that's right. Practically we don't know enough yet to build it, which is why the initial pilot deliberately doesn't try.

---

## 3. Architectural patterns surveyed

### The shared-context-layer concept

What we'd ideally want: a persistent, queryable representation of "the system" — code structure, dependency graph, architectural patterns, domain concepts, test conventions — maintained by experts and used by all the AI consumers.

Names floating around the industry: "context engineering," "agent grounding," "code knowledge base." The vocabulary isn't fully settled.

### Two dominant patterns in practice

**Hybrid indexing.** AST/code-graph parsing for structural truth, plus vector search for semantic similarity. Combined they let an agent both navigate the dependency structure and find functionally similar code. MCP (Model Context Protocol) is emerging as the standard way to expose this to agents.

**Knowledge graphs over code.** Explicit nodes and edges for files, functions, services, dependencies, sometimes enriched with docs and architectural decision records. Open-source examples: Graphify, Graph-Code. Commercial implementations include Greptile and Augment Code.

In serious deployments these tend to combine.

### The hard part: cross-repo

Most off-the-shelf tooling assumes a monorepo. We have ~20 repos. Cross-repo coordination — knowing which repos depend on which, what merge order makes sense, who maintains the dependency graph — is where most published rollouts hit walls.

The mabl writeup on doing this across 75+ repos is the closest published account to our situation. (See reading list.)

---

## 4. Tooling landscape, per use case

Snapshot of options as of early 2026. Will go stale; that's expected.

### Use case 1: Unit test coverage

- **Diffblue Cover** — Java only. Reinforcement-learning-based, deterministic. Their pitch: autonomous (not assisted) test generation, claimed 20x productivity over LLM-assisted approaches. Read their benchmark skeptically (it's their own) but the framing of autonomous vs assisted, and determinism vs LLM, is genuinely useful.
- **Qodo Gen** (formerly CodiumAI) — LLM-based, more language-flexible, weaker for deep Java cases.
- **Claude Code with custom skills** — most customizable, framework-agnostic.
- **GitHub Copilot** — inline completion, weaker at full-test-suite generation.

If we're not Java-heavy, Diffblue's relevance drops sharply. If we are, the autonomous-vs-assisted framing is worth understanding even if we don't end up using Diffblue.

### Use case 2: Application-wide refactoring

This is where the knowledge-graph approach shines — the agent needs to see *all* the variants to know they're variants.

- **Augment Code** — context engine across hundreds of thousands of files
- **Sourcegraph Cody** — codebase-indexing pioneer
- **Greptile** — full-codebase indexing, more known for review than refactoring but applicable
- **Tabnine Enterprise** — interesting because it can be fine-tuned on the entire codebase, training a specialized model on our patterns
- **Open source: Graphify, Graph-Code** — knowledge graph builders that could feed Claude Code or Cursor

### Use case 3: AI code reviews on PRs

The headline tradeoff: depth vs noise.

- **CodeRabbit** — quieter, ~44% bug catch rate per published benchmarks, very few false positives. Multi-platform (GitHub, GitLab, Bitbucket, Azure DevOps).
- **Greptile** — full-codebase indexing, ~82% catch rate, more false positives. Self-hostable.
- **Qodo Merge** — built on open-source PR-Agent. Self-hostable. Multi-platform.
- **Bito** — cheaper, more conversational, IDE-friendly.
- **BugBot** — tightly integrated with Cursor.
- **SonarQube** — bolted-on AI on top of existing SAST if we already use it.

### Use case 4: E2E API testing + test data

Least mature category. Most likely to require custom build.

- **Altruist's "API Agent" writeup** — closest published reference architecture: takes natural-language test specs from TestRail, validates them, generates request/response models, service methods, DB validation queries, creates merge requests. Worth reading carefully.
- **mabl, TestSprite, UiPath** — commercial offerings, more UI than API focused.
- **APITestGenie** — open source, OpenAPI-spec-driven.
- **Test data management itself** — separate hard problem, mostly hand-waved by vendors. Likely needs its own strategy (synthetic data, anonymized prod snapshots, fixture-as-code, contract-driven generation).

### Use case 5: Jira grooming and complexity estimation

- **Atlassian's MCP server** — lets agents read/write Jira directly. Likely the foundation for whatever we do here.
- **`jira-ticket-estimator`** Claude Code skill — open source, does codebase reconnaissance, scores complexity on five factors (scope, technical complexity, testing, risk/unknowns, dependencies). Concrete reference example.
- **Atlassian Intelligence (Rovo)** — built-in, mixed reviews for engineering complexity estimation specifically.
- **Better Estimates, Troopr AI** — third-party Jira plugins.

The hard part isn't the LLM — it's giving it our historical velocity data and our codebase knowledge in the same context.

### Use case 6: Bug triage

Mostly derivative of other capabilities — agent with access to code knowledge base + error logs + customer support history + Jira does what we'd want. Few dedicated products. Sentry and Honeycomb have AI assists from the observability side rather than the report side.

### Use case 7: Pair programming

Mostly an end-user-tool choice. Cursor, Claude Code, Augment Code, Sourcegraph Cody, Continue.dev (open source), Windsurf, Tabnine. They increasingly support custom MCP servers, so much of the choice becomes "which works best with our context layer?" rather than "which is best in isolation?"

---

## 5. Lessons others have hit (worth absorbing)

- **Security and governance, not AI maturity, is the bottleneck.** From Coder's enterprise interviews: getting agents working in the enterprise is mostly about security and governance, not AI maturity. Plan early for code-leaving-environment policies, secrets handling, audit trails. These will gate vendor choices later.

- **Invisible costs dominate.** Stanford's analysis of 51 enterprise AI deployments: 77% of challenges came from invisible costs (change management, process documentation). If we nail the tooling and skip the rituals (how prompts get shared, how knowledge gets captured, who owns what), we get disappointing results.

- **Bottom-up adoption beats top-down rollouts.** Most enterprise success stories had developers pulling tools in while leadership was still procuring the official thing. Worth deciding consciously how much we lead vs follow.

- **Measurement is its own problem.** GitHub Copilot's analytics tell you acceptance rates but not whether the AI helped ship better code. Diff-mapping (identifying AI-generated lines and tracking downstream outcomes) is emerging but not standard. We'll need to think about how we know whether anything is working.

- **Determinism is a real axis.** LLM-based tools are non-deterministic. For regulated/compliance-heavy code that matters. Diffblue's argument here is real even if the marketing numbers are aggressive.

---

## 6. Strategic decisions deferred to later

These are the forks we'll face once the pilot tells us what we want. Naming them, not solving them:

- **Central knowledge layer: build, buy, or hybrid?** Buy = commit to one vendor's index. Build = graph layer + MCP servers. Hybrid usually wins but costs more to maintain.
- **Self-hosted vs SaaS** — driven by code sensitivity. Most serious vendors offer self-hosted at higher tiers.
- **One vendor vs best-of-breed.** Single platform = cleaner. Best-of-breed = better per use case. Multi-vendor coordination is non-trivial.
- **Who owns the knowledge layer if we build one?** Pattern that works elsewhere: architects + small platform team. "Everyone contributes" tends to bit-rot.
- **Test data strategy** — its own discipline. Most AI testing vendors hand-wave it.
- **How we measure outcomes.** Diff-mapping? Self-reported? Cycle-time deltas? Worth picking something even if it's rough.

---

## 7. The meta-discussion: how we approached the pilot

This is the back-and-forth that shaped the position paper. Capturing the tensions and how they resolved.

### "Throwaway prototype" framing

**Initial proposal:** build something explicitly throwaway, useful while piloting, but not committed to long-term.

**Pushback:** "Throwaway" is too strong. If the prototype is useful, people will rely on it. If we don't maintain it, it'll degrade and embarrass us. Also, "throwaway" sounds wasteful to managers.

**Resolved framing:** "We are experimenting, with the expectation that after we learn what's going on and gain experience, we will go back and augment with commercial tools and/or custom development." This is more honest and a better sell — managers hear deliberate progression, not playing around.

### "No vendor tools" framing

**Initial proposal:** open source only at the start.

**Pushback:** That's incomplete — we'll use general-purpose AI tools we already have access to (Claude, Copilot, IntelliJ AI, the Atlassian stack). The thing we're avoiding in the pilot is *specialized AI tools that require expertise to evaluate and set up* (Diffblue, Greptile, Augment, etc.).

**Resolved framing:** The pilot uses general AI tools we already have, plus open source where useful. Specialized vendor tools come later, evaluated based on what we learn.

### "We are all learning" framing

**Considered:** explicitly stating "we are all learning together."

**Decision:** don't. It's condescending to experienced developers and would backfire. Better framing: "as an organization we have not done these tasks with AI before, so we need to gain experience while still deriving benefit."

### Champion dependency

**Concern raised:** most grassroots efforts die when the champion gets busy or leaves.

**Response:** [Champion] is lead of DevOps; making developers more productive and providing them with effective tools is part of the role. Championing this is not extra-curricular; it's the job. Champion dependency is a non-issue here. (Worth revisiting later if/when this grows beyond one person's capacity to sustain.)

### The 1.5x / 5x-10x value prop

**Framing settled on:** modest gains during the pilot phase (10-20% on the work of people who participate would justify the time), with vendors *claiming* much larger gains for specialized tools (5-10x; Diffblue claims 20x). The bet is small gains now plus the experience to make a much better decision about what specialized tooling is worth investing in.

The 5-10x is *vendor claim* territory. It's not our claim, and we shouldn't promise it. But it gives motivation for what could come next.

### Two on-ramps, not two parallel tracks

**Initial framing:** two parallel pilots on unit tests and PR reviews.

**Reframing:** two independent on-ramps. Pick the one you care about. No expectation that anyone does both, no requirement that the two efforts coordinate. If a third group forms organically around something else, that's fine. The point is to use interest to drive contribution, not to dictate scope.

This protects energy of the people who actually drive things, and reinforces the grassroots message.

### Document structure

Three documents:

1. **Brainstorming notes (this document)** — capture, not polished. Ongoing.
2. **Position paper** — 3 pages. Permission slip + first concrete bets + pointer to reading list.
3. **Reading list** — separate, audience-tagged, evolves over time.

---

## 8. Open questions / things we don't know yet

Honest list. Add to it as new questions surface. Move items to "what we learned" as they get answered.

- What context does the AI actually need that isn't easily provided? (We won't know until we try.)
- For unit tests: what fraction of AI-generated tests do we end up keeping? What's the rework cost?
- For PR reviews: what false-positive rate is tolerable? Does it depend on PR type?
- How do we share prompts and findings? Wiki space? Repo? Slack channel? Some mix?
- How do we measure "this helped" without it becoming overhead? Self-report? Time tracking?
- Where does the central knowledge layer live conceptually if we eventually build one? Per-repo? Cross-repo? Service-level?
- Test data: is this a problem we tackle in the pilot or defer entirely?
- How do we handle the "agent doesn't know our domain language" problem? Glossary? In-context loading? Fine-tuning?
- Privacy / sensitive code: which repos can we use with which tools? This will gate experimentation in some areas.
- When do we need to start thinking about champion succession, even if dependency is fine right now?
- What happens if the pilot fails to generate momentum after some reasonable time? Do we have a graceful exit?

---

## 9. Tensions we're holding (won't be resolved, just noting)

- **Determinism vs LLM.** LLM-based is more flexible, less reproducible. Specialized tools (Diffblue) are deterministic but narrower.
- **Signal vs noise in PR review.** Catching more bugs = more false positives. Each team will tolerate different points on the curve.
- **Build vs buy vs hybrid for the knowledge layer.** Decision deferred until we know what we want.
- **Centralization vs federation of the knowledge layer.** One layer for all 20 repos, or per-domain layers?
- **Vendor lock-in vs best-of-breed.** Tightly integrating with one vendor is easier but riskier.
- **Open source vs commercial.** Open source means more setup; commercial means less control.
- **Aspiration vs current state in the position paper.** We want people to be excited but not promise things we can't deliver.

---

## 10. To revisit / next iterations

- Tooling landscape will go stale; refresh every few months.
- Add tracking of what we've actually tried, what worked, what didn't.
- As open questions get answered, move them to a "what we learned" section.
- After first round of pilots, write up findings and feed into next position paper.

---

*Maintained by the DevOps team. Add to it. Edit it. Don't worry about polish.*
