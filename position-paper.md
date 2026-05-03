# AI for Software Development: A Pilot Proposal

## The problem

We have seven tasks where AI could help. Unit test coverage. PR reviews. Refactoring legacy "paste-and-modify" code. End-to-end API tests. Jira ticket grooming. Bug triage. Pair programming on real work.

All seven need deep understanding of the codebase. Conventions. Architecture. Domain. Test fixtures.

Building that understanding is expensive. Rebuilding it every time is worse. Right now, that's what we do. The agent doesn't know our patterns. A developer briefs it, gets one result, loses the context. The next task starts from zero.

There's a structural answer: a shared, persistent representation of our codebase that AI can query. But we don't know what to build. Specialized tools exist; evaluating them well takes expertise we don't have. Custom development takes a clear target we don't have either.

## What this is

A pilot. Small experiments with AI tools we already have. Claude. Copilot. IntelliJ AI. The Atlassian stack. Open source where it helps.

Two starting points: unit test coverage and AI-assisted PR reviews. We start with shared prompts and small examples. We capture what works.

We are not building the right system. We don't know what the right system is. We're building enough experience to evaluate one later — based on something other than vendor pitches.

This is experimental. Tools we build now will likely be replaced. Approaches will change. That's the design.

## What we expect

Modest gains during the pilot. Maybe 10-20% on the work of people who participate. Enough to pay for the time.

Vendors of specialized tools claim much more. Diffblue claims 20x for unit test generation. Greptile and CodeRabbit publish bug-catch rates of 80%+ and 40%+ for code review. Others claim 5-10x in their domains. Whether those numbers hold in our environment is unknown. That's why we pilot.

The bet: small gains now, plus the experience to make a much better decision later.

## Two on-ramps. Pick one.

Opt-in by interest. Contribute where you care. No obligation to do both.

**On-ramp A — Unit test coverage.** A small group experiments with prompts to produce useful tests against our actual code. We capture what worked, what didn't, what context the AI needed. We share results.

**On-ramp B — PR reviews.** A small group experiments with using AI for first-pass review — bugs, improvements, pattern violations. Humans running prompts manually at first. Lightweight automation later if it earns its keep. Same capture: what worked, what didn't, what was missing.

Interested in something else — refactoring, ticket grooming, bug triage? Say so. If a third group forms, fine. These two are starting points, not a mandate.

## What we're asking for

**From contributors:** time between other tasks. No dedicated allocation. Run an experiment. Share a prompt. Write a one-page note. Or just read what others share and react.

**From team leads and managers:** tacit permission. Not budget. Not headcount. Not a project plan. The same latitude given for tooling improvements and learning time.

## Success

This is a pilot, not a project. At the end:

- We've tried several distinct approaches in at least one on-ramp.
- We've shared what worked, what didn't, what the AI was missing.
- We have a clearer picture of what we'd want from a specialized tool or a custom build.
- Participants got modest productivity gains while contributing.

Success is **not** building the end system. Not picking a vendor. Not hitting a productivity number. Those decisions come later.

## Reading list

A separate document, `reading-list.md`, has resources tagged by audience — curious developer, experienced developer or skeptic, team lead or manager. Skim what's tagged for you. Ignore the rest.

## What happens next

If this lands: a shared place for prompts, notes, findings. People self-organize into one or both on-ramps. Say so if you're in.

The brainstorming notes (`brainstorming-notes.md`) capture the broader discussion — the seven use cases, what we surveyed, what others learned, what we don't know yet. Read them if you want the context behind this proposal.

---

*Maintained by the DevOps team. This document, the reading list, and the brainstorming notes will evolve as we learn.*
