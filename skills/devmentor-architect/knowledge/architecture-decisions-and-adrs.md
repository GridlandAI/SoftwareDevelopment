# Architecture Decisions and ADRs

**Domain:** 04 - Software Architecture Fundamentals
**Primary sources:** Software Architecture and Decision-Making (Perera, 2024); The Software Architect Elevator (Hohpe, 2020); Facilitating Software Architecture (Harmel-Law, 2024); 97 Things Every Software Architect Should Know (Monson-Haefel, ed., 2009)

## Overview
Architecture is, at its core, the practice of making and recording decisions. A useful working definition is that to practice architecture is to decide: a software architecture is a set of components plus how they work together, and the architecturally significant decisions are the ones that are hard or expensive to reverse, that shape cross-functional characteristics, dependencies, interfaces, and product/team alignment. Because these decisions are made at the moment of highest ignorance - the start of a project, when least is known about users, technology, and how the system behaves at the edges - the central challenge is making good decisions under deep uncertainty.

Three kinds of uncertainty complicate every architectural decision (Perera): a partial understanding of users and what they want; limited comprehension of how systems behave in new or complicated situations; and the certainty that requirements will change as users and use cases evolve. Good decision-making therefore means separating the *quality of a decision* from the *quality of its outcome* (you cannot judge a decision by an outcome you did not know at the time), reasoning explicitly about reversibility and trade-offs, deferring decisions to the last responsible moment, and capturing the rationale so the decision can be understood, revisited, and learned from later. Architecture Decision Records (ADRs) are the standard lightweight artifact for capturing that rationale.

## Core concepts

**The decision as the unit of architectural work.** Decisions, not diagrams, are the substance of architecture. A decision process has three distinct stages worth keeping separate (Harmel-Law): *option making* (crafting the set of options), *decision taking* (considering and selecting one), and *decision sharing* (communicating it to those who implement it). Most decision processes differ mainly in when and how they involve people across these three stages.

**What makes a decision "architecturally significant" (Harmel-Law).** A decision is architecturally significant if it materially affects the system's structure, cross-functional characteristics (the "ilities"), dependencies between components, interfaces/contracts, construction techniques, or how products and teams are aligned. Choosing a public API shape, a database schema deployed to customers, an event-driven style, or a service boundary is significant; choosing an internal variable name is not. Notably, "not all decisions with an architectural impact start as 'architectural' decisions" - significant decisions often emerge implicitly during coding and must be recognized and recorded retroactively.

**Reversible vs. irreversible decisions.** Conventional definitions stress that architecture is about difficult-to-reverse decisions made early. Fowler's inversion, quoted by both Hohpe and Perera, reframes the job: "one of an architect's most important tasks is to eliminate irreversibility in software designs." The best decision is often the one you do not have to make yet, because you have built in the option to change it later. Perera's rule: delegate any decision that is reversible without significant cost; reserve real deliberation for the few decisions that are both *critical* and *irreversible*. A decision is critical when the outcome differs significantly between the best and the obvious option - not merely when the price tag or visibility is high.

**Decision quality vs. outcome (Hohpe).** Winning the lottery is a good outcome from a bad decision; crossing a red light drunk with eyes closed and surviving is a good outcome from a bad decision. You must judge decisions by the risk and information available at the time, not by the result. Humans are poor at this, especially with small probabilities and severe outcomes.

**Think deeply but implement slowly (Perera).** Design deeply the things that are hard to change - APIs exposed to customers, APIs of highly shared services, database schemas shipped to customers, shared message/object formats, technology frameworks - and put them through heavy review before release. But implement slowly: defer building anything that can evolve later without affecting the rest of the system. Designing deeply surfaces surprises and builds consensus early; implementing slowly avoids waste and lets you learn from evidence.

## Frameworks & models

**Perera's five questions (understand the terrain / business context).** Ask before deciding:
1. *When is the best time to market?* The business, not the architect, sets timing; design with the knowledge you can rewrite after the deadline.
2. *What is the skill level of the team?* "Go to war with the army you have." Do not pick CQRS or event-driven designs without people who have done them; prototype risky styles in the background.
3. *What is the system's performance sensitivity?* If the system runs near the limits of a naive architecture it is performance-sensitive and needs exotic techniques, deep precision, and continuous measurement; most systems are performance-insensitive (a simple Spring Boot + DB service handles hundreds of requests/sec).
4. *When can we rewrite the system?* Accept that successful systems get rewritten many times; build for the first 10,000–50,000 users and rewrite when you can afford to. This defines phase one and lets you defer hard-but-not-immediate problems.
5. *What are the hard problems?* Identify problems needing long-term work (especially those that are a competitive advantage) and start them early as parallel proofs of concept; do not procrastinate on them.

**Perera's seven principles (what / when / how to implement).**
1. Drive everything from the user's journey.
2. Use an iterative thin-slice strategy (a working end-to-end slice over big-bang integration; the Wright brothers' glider-first approach).
3. On each iteration, add the most value for the least effort (MVP, "when in doubt leave it out," wait for three people to ask, beware "Google envy" / overengineering).
4. Make decisions and absorb the risks (the chief architect sets concrete targets even amid unresolvable uncertainty - like the NASA moon-rover spec).
5. Design deeply things that are hard to change but implement them slowly.
6. Eliminate the unknowns by working on hard problems early and in parallel (experiments over arguments; instrument and monitor early).
7. Understand the trade-offs between cohesion and flexibility (every "good practice" has a cost; context is king).

**Expected-value / decision-tree reasoning (Hohpe, Perera).** Make uncertainty explicit by attaching probabilities to outcomes. Perera's portability example: if portability costs 50% more but there is only a 10% chance of needing it, expected cost of building portable ≈ 150%; if porting later costs 250% extra, expected cost of not building portable ≈ 100% + 250%×10% = 125% → choose non-portable. Hohpe's decision trees show how to value information itself (it can be worth paying an insider $150 to learn whether a rebate will happen). All models are wrong but some are useful; rerun with different probabilities to test whether the decision flips.

**Architecture as selling options (Hohpe).** Borrowing from finance, an option is "the right, but not the obligation, to execute a transaction at fixed parameters in the future." Good architecture creates options - coding in a portable language is an option to run on other operating systems, deferring that decision. Options have value (Black-Scholes), and crucially their value *increases with uncertainty* and with the time until they must be exercised. But options also have a cost (a "strike price"): interfaces and abstraction layers are options with real overhead. The architect's wisdom is having options *and* knowing when not to use them.

**The 70% rule (Perera, citing Bezos).** Make the decision when you have about 70% of the information, because you almost never have all of it; waiting for certainty is itself a costly decision. Measuring "70%" is intuitive, not mathematical.

**Jeff Bezos one-way vs. two-way doors (implicit in Perera's reversible/irreversible split).** Treat reversible ("two-way door") decisions as fast, delegated, and low-ceremony; treat irreversible ("one-way door") decisions with care, data, and experiments - but still decide rather than block the team.

## Patterns

**Architecture Decision Record (ADR)** - *Intent:* capture a single architectural decision with its context, options, consequences, and rationale in a short (~two-page) immutable document. *When to use:* for every architecturally significant decision; as a decision-support tool while deciding and as a historical record afterward. *Trade-offs:* requires discipline to keep lightweight and reader-focused; immutability means corrections come via superseding ADRs, not edits; low value if sections are filled half-heartedly. *Structure (Harmel-Law, after Nygard/Tyree-Akerman):* ID & Title (decision stated in the title), Status & Date, Author (accountable party), Decision (sparse declaration of intent), Context (forces and criteria at the time), Options considered (selected option first; "only one option" is a smell), Consequences (why selected/rejected - include drawbacks), and Advice (raw inputs from advisers). Place the Decision high and Context lower, because the reader usually wants the decision first.

**Defer with Options / Last Responsible Moment** - *Intent:* avoid premature irreversible commitments by building flexibility that localizes future change. *When to use:* high-uncertainty decisions where more will be known later and the cost of the option is justified. *Trade-offs:* abstractions, interfaces, and modular designs carry runtime and cognitive cost; too many abstraction layers is an anti-pattern; deciding "modular" is itself an up-front decision.

**Thin Slice / Walking Skeleton** - *Intent:* build a minimal end-to-end working system first, then profile, find bottlenecks, and add complexity only where needed. *When to use:* almost always, unless there is a specific reason to optimize early. *Trade-offs:* requires early integration discipline; some genuinely performance-sensitive systems need exotic design from the start. (See 97 Things: "Start with a Walking Skeleton.")

**Make Decisions and Absorb Risk** - *Intent:* the chief architect sets concrete targets under irreducible uncertainty so the team can progress. *When to use:* when nobody knows the "right" number (load, latency) and indecision is blocking delivery. *Trade-offs:* concentrates accountability; must be paired with experiments and willingness to revise.

**Expired / Time-Bounded ADR** - *Intent:* record a decision that should be revisited after a set period (e.g., go with a non-preferred vendor for six months). *When to use:* time-sensitive choices made under temporary constraints. *Trade-offs:* a nonstandard status; needs a process to actually revisit and write the superseding ADR.

## Principles & best practices
- **Record your rationale; take responsibility for your decisions** (97 Things). The "why" is more durable and more valuable than the "what."
- **Always have more than one option.** A single-option ADR is a bad smell; at minimum there is always "do something" vs. "do nothing." Capture killer drawbacks of rejected options.
- **Separate likelihood from impact.** Many IT decisions (security, outages) are low-probability/high-severity; baseline probabilities and strip out emotion to decide rationally (Hohpe: consider a "microfail," a one-in-a-million chance of catastrophic failure).
- **Beware decision biases** (Hohpe, via Kahneman): confirmation bias (reverse-engineered scorecards and traffic-light tables that protect the status quo), loss aversion / prospect theory (declining +EV gambles, avoiding beneficial refactors), priming (anchoring on irrelevant recent numbers), and the law of small numbers (celebrating a lucky low-incident week). Numeric scores beat traffic-light tables, but an 82.1-vs-79.8 "winner" is rarely statistically meaningful.
- **Defer decisions to the last responsible moment, but no further.** Avoidance is itself a decision; when a decision is genuinely needed, decide.
- **Use ADRs as a decision-support tool, not just a log.** Write the Context, Options, and Consequences while deciding, revisiting sections out of order, to think the decision through.
- **Keep ADRs immutable and curated.** Store them in developer tooling (markdown in source control, a wiki next to dev docs, or a work-ticketing system), give each a unique ID and a decision-bearing title, and maintain a reverse-chronological change-log page as the architecture's history.
- **Quantify and prefer working code to specification** (97 Things: "Quantify"; "One Line of Working Code Is Worth 500 of Specification"). Test ideas with fifteen minutes of code rather than argument.

## Anti-patterns & pitfalls
- **Analysis paralysis** - requirements gathering drags on while no code is written, often because all critical decisions are forced up front under maximum ignorance.
- **Premature irreversibility** - locking in early, expensive-to-reverse decisions (often driven by procurement, budget cycles, or vendor pressure rather than technical need).
- **Judging decisions by outcomes** - rewarding lucky bad decisions and punishing unlucky good ones.
- **Reverse-engineered scorecards / traffic-light tables** - comparison charts built backward from a preferred outcome, or that demand quirks only the incumbent product has.
- **Single-option ADRs** and **half-filled templates** - signals that the decision was not really explored or the template does not fit the org.
- **Over-optioning / too many abstraction layers** - paying option premiums (complexity, performance) for flexibility that is never used ("Google envy," overengineering).
- **Blocking the team for non-critical decisions** - withholding a reversible decision instead of delegating it; or refusing to set a target under uncertainty.

## Decision guidance
An agent reasoning about an architectural decision should:
1. **Classify the decision.** Is it architecturally significant? Is it reversible (two-way door) or irreversible (one-way door)? Is it *critical* (best vs. obvious option differs greatly)? Only critical + irreversible decisions warrant heavy deliberation; delegate the rest.
2. **Establish the business context first** using Perera's five questions (time to market, team skill, performance sensitivity, rewrite horizon, hard problems) before reaching for patterns.
3. **Reason in expected value under uncertainty.** Attach rough probabilities, sketch a decision tree, value the option of deferring and the value of buying more information; rerun to see if the decision flips. Decide at ~70% information.
4. **Prefer eliminating irreversibility.** Build options (interfaces, modularity) where uncertainty is high and the option cost is justified; deepen design only for the genuinely hard-to-change surfaces (public APIs, shared schemas, frameworks) and implement everything else slowly via thin slices.
5. **Guard against bias.** Separate likelihood from impact, distrust scorecards reverse-engineered from a preferred answer, and do not over-weight small samples.
6. **Capture the decision in an ADR.** State the decision in the title, record context/forces, list multiple options with their killer drawbacks, document consequences (including downsides of the chosen option), and keep the record immutable - supersede rather than edit.
7. **Decide and absorb the risk.** When uncertainty is irreducible and the team is blocked, set a concrete target, communicate it plainly, and revise as evidence arrives.

## Source attribution
- *Five questions, seven principles, three kinds of uncertainty, think-deeply/implement-slowly, reversible vs. irreversible and the 70% rule, make-decisions-and-absorb-risk, expected-value portability example, thin slice / walking skeleton, communicating and evolving the design:* Software Architecture and Decision-Making (Perera, 2024), Chapters 1, 2, and 13.
- *Decision quality vs. outcome, biases (Kahneman: confirmation, loss aversion, priming, law of small numbers), decision trees and the value of information, model thinking, architecture as selling options, options increase in value with uncertainty, eliminating irreversibility (Fowler), avoiding decisions:* The Software Architect Elevator (Hohpe; Brown; Knott, 2020), Chapters 6 and 9.
- *Decision as the unit of work, the three decision stages, architecturally significant decisions, ADR structure / lifecycle / management / curation, expired and other statuses:* Facilitating Software Architecture (Harmel-Law, 2024), Chapters 2, 3, and 6.
- *Record your rationale, take responsibility for decisions, quantify, walking skeleton, working code over specification:* 97 Things Every Software Architect Should Know (Monson-Haefel, ed., 2009).

## Recent developments (2024–2026)

- **AI-assisted ADR authoring went mainstream.** A wave of 2024-2025 tooling now drafts ADRs from a codebase or a design discussion: agents that scan a repository and emit ADRs automatically, ADR-writer agents built on LLMs, and free generators (e.g., Workik). The recurring caution mirrors this file's quality bar: AI can produce the *what* cheaply, but a human still has to supply genuine context, real rejected options with killer drawbacks, and honest consequences, or the ADR becomes a half-filled template. (Adolfi.dev, "AI generated Architecture Decision Records," 2025, https://adolfi.dev/blog/ai-generated-adr/; Strengholt, "Building an Architecture Decision Record Writer Agent," Medium, 2025, https://piethein.medium.com/building-an-architecture-decision-record-writer-agent-a74f8f739271)

- **ADRs as deterministic guardrails for AI coding agents.** New enforcement tools (e.g., Mneme HQ) convert ADRs into pre-generation checks for Claude Code, Cursor, and Copilot, so a recorded decision actively blocks an agent from violating it. This directly attacks the classic "no one consults the ADR under deadline pressure" failure by moving the decision into the code-generation path. (adr.github.io tooling, 2025, https://adr.github.io/adr-tooling/)

- **MADR + Log4brains remain the docs-as-code default.** The MADR (Markdown Any Decision Records) template shipped with Log4brains continues to be the common baseline; Log4brains publishes ADRs from the IDE to a static site and adds tags and a draft status for collaborative writing, reinforcing the "store ADRs in developer tooling" guidance here. (Log4brains GitHub, 2024-2025, https://github.com/thomvaill/log4brains)

- **The decentralized-decision angle gained traction.** Harmel-Law's architecture advice process (the source of this file's three decision stages and ADR-as-flow framing) became a widely discussed practice in 2025-2026, repositioning ADRs as the *medium* of decentralized decision-making rather than just a log. (InfoQ, "Decentralizing Architectural Decisions with the Architecture Advice Process," 2026, https://www.infoq.com/news/2026/03/architecture-advice-process/)

- **Conceptually stable.** The underlying decision theory (reversibility, decision-vs-outcome quality, expected-value reasoning, the 70% rule, one-way/two-way doors) is unchanged from Perera/Hohpe; the 2024-2026 shift is automation of authoring and enforcement and the move toward decentralized decision processes.
