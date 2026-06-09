# Pragmatic & Effective Architecture Practice

**Domain:** 04 - Software Architecture Fundamentals
**Primary sources:** Master Software Architecture - A Pragmatic Guide (Maciej "MJ" Jedrzejewski; 2024, Leanpub); Effective Software Architecture - Building Better Software (Oliver Goldman; 2024, Addison-Wesley)

## Overview
This topic covers how a working architect actually operates in a modern, evolving product organization: how to start an architecture, balance rigor against speed, make and record decisions, communicate, and let the architecture evolve. Where the iSAQB curriculum supplies vocabulary and Lowy supplies a prescriptive method, these two books supply the day-to-day *practice* - the heuristics, checklists, and processes that keep architecture useful rather than ceremonial.

Both authors agree on the central modern stance: architecture is not a one-time artifact but a continuous, holistic process that evolves with the business; there are no silver bullets; every decision is a trade-off made in a specific context; and the architect is a hands-on team member and connector, not an ivory-tower authority. Jedrzejewski organizes this as a seven-step journey from understanding the domain to evolving the system. Goldman organizes it around change as the unit of work, with a lightweight process (vision, change proposals, backlog), disciplined decision-making, and supporting practices (catalogs, templates, reviews) - all relentlessly optimized for the *reader* and for keeping the work moving.

## Core concepts

**Architecture as a holistic, evolving process (Jedrzejewski).** "A set of patterns, principles, processes, and relationships that make up the overall design." It is never static - business change almost always equals architecture change. Treat the whole as more than its parts: business analysis, infrastructure, deployment, solution architecture, test strategy, release strategy, and teams all get attention. The architect is a "construction manager," not an expert in every trade.

**Architecture as the long-term, change-governing dimension (Goldman).** Architecture is the fundamental organization of a system's components, their relationships to each other and the environment, the principles governing its design, *and its evolution over time*. "An architecture team that does not pay attention to evolution simply isn't practicing architecture." Every component and relationship persists until some change removes it; there are no truly "short-term" architectural changes.

**Architectural drivers (Jedrzejewski).** Four interdependent groups that should drive every decision instead of feelings or fashion: **functional requirements** (what the system must do), **business constraints** (budget, deadline, resources, regulation like GDPR), **technical constraints** (mandated stack/platform/integration), and **quality attributes** (maintainability, complexity, scalability, performance, availability, etc.). Changing one forces a change in another (earlier deadline → fewer features; higher availability → more infra and longer deadline).

**Architecturally significant requirements & dependability (Goldman).** Not all requirements shape the architecture; focus on the architecturally significant ones. "Dependability" (reliability, availability, security, privacy, etc.) is a recurring quality theme captured in templates and reviewed for every change.

**Stages and types of change (Goldman).** Each change moves through three logical stages - **motivation** (why), **concept** (what/which components), **detailed design** (how) - though not always linearly. Changes are product-driven or technology-driven, and can add, remove, or modify; we wrongly assume changes are always additive.

**Pragmatism + holism (Jedrzejewski).** Pragmatism: address current conditions and observed/imminent problems, not predefined theory or hypothetical futures; start lean (build an MVP in weeks), react to real data, but always leave room to grow. Holism: give every architectural element comparable focus; a leak in any one (e.g., security) sinks the whole.

**Simplicity and the investment mindset (both).** Goldman: simplification is an "unalloyed good"; treat every change as an investment judged by its return across performance, cost, dependability, quality. Jedrzejewski: "simple does not mean silly"; keep the architecture as simple as the requirements allow.

## Frameworks & models

### The seven steps of pragmatic architecture (Jedrzejewski)
1. **Understand software architecture** - definition, architectural drivers, the architect's role, pragmatism, holism.
2. **Discover your business domain** - high-level and in-depth workshops (event storming, domain storytelling); define **bounded contexts** with their inbound/outbound communication (events, commands, queries) using a context canvas.
3. **Recognize the environment** - what you're building, how product decisions are made, team strengths/size, other teams, infrastructure teams, existing infrastructure, scale/numbers, budget, deadline, expectations.
4. **Choose a deployment strategy** - single vs. multiple deployment units; communication styles; **modular monolith** vs. **microservices**.
5. **Define a release strategy** - release vs. deployment; deployment strategies (blue-green, canary, rolling); continuous delivery vs. continuous deployment; a pragmatic middle ground.
6. **Focus on testing** - shift-left testing, the testing pyramid and its variations, performance and penetration testing, cost-of-fixing-bugs curve.
7. **Evolve your architecture** - the project paradox, common pitfalls, and a four-step evolution (simplicity → maintainability → and beyond), driven by observation and architectural drivers.

### Quantify the scale before choosing infrastructure (Jedrzejewski)
Estimate concrete numbers (users × requests/day, employees × requests/day → requests/second at peak) before adding components. Most systems need far less than the architect imagines (e.g., a few requests/second → no complex infra; standard cloud gives 99.5% availability). "Build your architecture based on what you need now, not what you might need in the future."

### The four WHY/WHAT/HOW/WITH questions (Oskar Dudycz, in Jedrzejewski)
A decision *process*, not a rulebook: **WHY** (product vision, business model, where money flows) → **WHAT** (the business workflow/mental model; event storming, domain storytelling) → **HOW** (requirements/guarantees, the *class* of solution - database type, deployment type, integration patterns, not specific tech; tools like C4) → **WITH** (specific tooling meeting functional and non-functional needs and matching team experience). Then rinse and repeat. Record decisions *and discarded ideas* with context; "optimize for removability, not just maintainability."

### The change-driven process (Goldman)
- **Document the system** - keep accurate, current descriptions; redocument after each change so no one needs the change history to understand the system.
- **Work toward a vision** - an architectural vision describes the desired state in a 3–5 year horizon; concrete but not overly specific; ~6 pages; refreshed roughly annually. Its value is *alignment* of many simultaneous changes, not being "right."
- **Write change proposals** - the core unit of work; a container that grows from a sentence (motivation/concept) to a full design; captures every proposed change (even meta-changes to the process itself).
- **Maintain a backlog** - the list of current/past/future change proposals with their stage; distinct from the product backlog (no 1:1 mapping).
- **Consider alternatives** - generate multiple concepts per motivation and multiple designs per concept; rejecting most is healthy and increases predictability by surfacing issues early.
- **Not doing things / urgent vs. important** - deciding *not* to do something is a first-class outcome; separate urgency from importance.

### Decision-making heuristics (Goldman) - the questions to ask
- **Will more information help?** Gather *just enough* to make a non-reversible decision; excess info only adds speculative noise and slows you down.
- **What's happening in the meantime?** The status quo persists while you deliberate; if other teams are integrating against the current state, deciding fast shrinks the cost of the change.
- **How many decisions are in play?** Split or merge correctly - separate a temporary mitigation from a permanent fix; unify two teams' duplicate efforts into one decision; but keep "whether" separate from "which technology."
- **What's the cost of not doing it?** The status quo is rarely free (maintenance, vulnerabilities, cloud costs, esoteric-language upkeep). Humans under-weight ongoing costs vs. one-time costs.
- **Can I live with this change?** Fast-but-lower-quality vs. expensive-but-better: teams cannot commit to future rework, so a "fix it later" compromise means living with the fast option forever. Aim for the "moderately-expensive-and-we-can-live-with-it" middle.
- **What is the cost of getting this wrong?** Often there's no single right answer (naming); when a decision is cheap to change, decide quickly and correct later - for low-cost-of-change/high-uncertainty decisions, just proceeding can beat deliberating.
- **How much more certain can I be? / Is this my decision to make? / Am I aligned? / Can I document it?** - round out the checklist.

### Supporting practices (Goldman)
- **Backlog** as externalized team memory: capture every idea (forget it safely), keep rejected items as "closed," prevent duplicates, curate "up next" (5–10 items), housekeep 3–4× per year. Optimize for readers, not authors.
- **Catalogs** of software components and data models documenting the *current* state.
- **Templates** for change proposals: accelerate writing and reviewing and act as a *checklist* (prompting for security, privacy, dependability, and other quality attributes). Multiplicative productivity effect over hundreds of documents.
- **Reviews, status, velocity, thinking** as deliberate practices.
- **Architecture Decision Records (ADRs):** useful but trade author convenience for reader burden; if you use them, still update the system specification after each decision so readers never need to replay the ADR history (like reading source by replaying commits).

## Patterns

**Modular monolith** - *intent:* one deployment/process, code logically split into high-cohesion modules (often 1:1 with bounded contexts), each ideally with its own DB schema. *When:* most greenfield applications; the pragmatic default. *Trade-offs:* simplicity of a monolith + modularity of microservices; but the whole must be redeployed and scaled together, and multi-team work can contend over shared building blocks.

**Microservices** - *intent:* independently deployable/scalable services aligned to bounded contexts. *When:* genuine need for independent deployment/scaling, team autonomy, or selective scaling at proven scale. *Trade-offs:* independent lifecycles and targeted scaling (huge cost savings when one function dominates load) vs. distributed-system complexity, higher entry threshold, and unstable early boundaries (you'll merge/split services in the first months). Usually *don't start here.*

**Vision paper** - *intent:* a 3–5 year, ~6-page statement of desired architectural state. *When:* to align many concurrent changes. *Trade-offs:* alignment and direction vs. speculation if it reaches beyond ~5 years.

**Change proposal** - *intent:* a growing document capturing motivation → concept → design for one change. *When:* every architectural change (and meta-changes). *Trade-offs:* lightweight capture + alternatives + traceability vs. minor overhead; the backlog of proposals doubles as documentation in early stages.

**Architecture backlog** - *intent:* externalized, searchable memory of all change proposals and their stages. *When:* always. *Trade-offs:* lets the team forget safely and plan ahead; requires the discipline to capture, dedupe, and housekeep.

**Templates + catalogs** - *intent:* standardize proposals and document current components/data models. *When:* any sustained practice. *Trade-offs:* consistency and built-in quality checklists vs. the discipline to keep them current and reader-optimized.

## Principles & best practices
- **Start lean; evolve, don't revolt.** MVP in weeks; add components (cache, monitoring, scaling, streaming) only when real traffic/feedback demands them. Evolution, not revolution.
- **Let architectural drivers, not fashion, decide.** Resist copying FAANG patterns or conference hype; what works for them may fail at your scale.
- **Defer decisions (the project paradox).** Knowledge is lowest when the most decisions must be made; defer as many as possible until understanding grows; invest more upfront in understanding *why* and *what* to build.
- **Optimize documentation for the reader** and for the *current* state of the system; redocument after each change.
- **Decide in parallel with a moving system.** Speed reduces disruption when others integrate against the status quo.
- **Treat every change as an investment** judged by return; favor simplification; reframe "short-term fixes" in architectural terms (new relationships raise testing, maintenance, coupling, and reduce dependability).
- **Authority vs. accountability.** Architects have accountability, not authority; contribute insight, debate options on merits, find better outcomes - don't "win." Frame debates as options/trade-offs, never as people or roles vs. each other.
- **Deliver incrementally.** Most changes should be narrow and fast; resist scope-snowball ("while we're here, let's rework B, C, D"); the bigger the change, the more we underestimate cost and overestimate benefit.
- **No silver bullets; everything is a trade-off** chosen for the current context, often by combining concepts.
- **The architect is a connector** (Hohpe's "architect elevator" between engine room and board room), mentor, and hands-on contributor who can say "stop" to any audience and explain complex ideas simply.
- **Modularity is the chief tool against uncertainty** (Vlad Khononov, in Jedrzejewski): make assumptions explicit by encapsulating each in a module so future change touches one known module.
- **Test shift-left**; cheaper to fix bugs early; use the testing pyramid; include performance and penetration testing.

## Anti-patterns & pitfalls
- **Too complex from the start (~50% of cases, Jedrzejewski).** Microservices/Kubernetes/cache/streaming for a few thousand users → many failure points, high entry threshold, high infra cost (one case reducible 90%, $100k→$10k), distributed-system pain, and boundaries that shift anyway. Realization usually comes too late to simplify cheaply.
- **Too trivial for too long (~30% of cases).** A shortcut MVP (everything in one `Products` table) never refactored → a "big ball of mud" with dozens of special-case rules, severe performance and maintenance problems, twice-a-year releases, and eventual collapse. Initial success ≠ eternal glory.
- **"Short-term" architectural fixes** that are never cleaned up; they accumulate dependencies, slow work, increase schedule pressure, and beget more fixes - the long-term effect of short-term fixes is killing projects.
- **Scope snowball / big-bang re-architecture** that delivers nothing while expanding endlessly - a hallmark of immature teams.
- **Fast-but-lower-quality compromises** sold as "fix it later" - teams can't commit future capacity, so you live with the fast version permanently.
- **Over-investing in cheap-to-change/low-stakes decisions** (e.g., names with equally good alternatives) and gathering excess information that only delays.
- **Treating technical debt like good financial debt** - it's not an investment generating returns; it's accumulated under-investment that *raises* ongoing costs and makes "paying it off" harder (more like unaffordable credit-card debt).
- **Confusing authority with accountability** - "I'm the architect!" tantrums; asserting authority instead of contributing insight.
- **Relying only on ADRs/change history** for documentation - forces every reader to replay decisions and reconcile overrides.
- **Imposing a separate toolset** from product/engineering colleagues, distancing the architecture team from its key collaborators.

## Decision guidance
- **Sequence the work (Jedrzejewski):** understand the domain (bounded contexts) → recognize the environment and quantify scale → pick deployment (default modular monolith; microservices only with a proven need) → release strategy → testing → and evolve continuously.
- **Run decisions through the WHY/WHAT/HOW/WITH process** and Goldman's question checklist (information value, what's-in-the-meantime, number of decisions, cost of inaction, can-I-live-with-it, cost of being wrong, certainty, ownership, alignment, documentability).
- **Calibrate rigor to stakes and reversibility:** decide cheaply-reversible/uncertain things fast and correct later; invest more only in expensive-to-change decisions (e.g., primary language). Most decisions need neither formal tracking nor approval.
- **Default to simplicity and incremental delivery;** add complexity only against observed or imminent need backed by real numbers; defer decisions until knowledge grows.
- **Make assumptions explicit as modules;** prefer designs you can remove pieces from (removability).
- **Manage everything as change proposals in one backlog;** consider alternatives; keep documentation reader-optimized and current; use templates as quality checklists.
- **Lead through accountability and alignment, not authority;** debate options and trade-offs, never people; align everyone behind a shared vision.

## Source attribution
- **Holistic/evolving definition, architectural drivers, pragmatism & holism, the architect's role, seven-step journey, scale estimation, modular monolith vs. microservices, project paradox, four pitfalls, four-step evolution, deployment/release/testing strategies, expert essays (Khononov, Jovanović, Dudycz, Milanović, Čahuk):** Master Software Architecture (Jedrzejewski 2024), Steps 1–7.
- **Architecture as change/evolution, stages & types of change, simplicity & investment mindset, incremental delivery, authority vs. accountability:** Effective Software Architecture (Goldman 2024), ch. 1–3.
- **Process - document the system, vision papers, change proposals, backlog, alternatives, not-doing-things, urgent vs. important:** Goldman, ch. 4.
- **Design - decomposition/composition, incrementalism, parallelism, work in the open, giving up, done:** Goldman, ch. 5.
- **Decision-making heuristics (the questions), technical-debt critique:** Goldman, ch. 6.
- **Practices - backlog as memory, catalogs, templates as checklists, reviews/status/velocity, ADR caveats:** Goldman, ch. 7.
- **Communication - mental models, writing/talking, information architecture, naming, dictionaries, listening; architecture & product teams:** Goldman, ch. 8–10.

## Recent developments (2024–2026)

- **"Start simple, default to modular monolith" became the industry consensus.** The 2024-2026 discourse strongly validated both books' central advice: the modular monolith is now widely treated as the pragmatic default, with reporting that ~42% of organizations that adopted microservices consolidated services back into larger units due to operational and debugging overhead. Jedrzejewski's "too complex from the start (~50% of cases)" pitfall is now a mainstream cautionary tale. (SoftwareSeni, "From Microservices Consolidation to Modular Monoliths," 2025, https://www.softwareseni.com/understanding-modern-software-architecture-from-microservices-consolidation-to-modular-monoliths/; Foojay, "Monolith vs Microservices in 2025," https://foojay.io/today/monolith-vs-microservices-2025/)

- **Developer-experience ("vibe coding") reinforces centralized defaults.** A 2025 theme is that the focus on great developer experience favors monoliths and monorepos (one repo, one build, one entry point, smoother hot reload and local testing), aligning with Goldman's optimize-for-the-reader and Jedrzejewski's start-lean stances. (Java Code Geeks / "atomic architect" pieces, 2025, https://medium.com/@the_atomic_architect/architecture-patterns-that-actually-scale-in-2025-the-only-three-you-need-89d1488c60a7)

- **AI changes the unit of work and decision cadence.** As LLMs generate code and even draft change proposals/ADRs, the architect's emphasis shifts further toward Goldman's decision-quality questions (cost of being wrong, can-I-live-with-it, verification) and validating probabilistic output; the InfoQ 2025 trends report and "skills gap in the LLM era" research frame this as the new core practice. (InfoQ Software Architecture and Design Trends Report 2025, https://www.infoq.com/articles/architecture-trends-2025/; SSRN, 2025, https://papers.ssrn.com/sol3/papers.cfm?abstract_id=5658241)

- **Decentralized decision-making went mainstream.** Harmel-Law's architecture advice process became a widely adopted way to live out the "accountability not authority" and "connector, not commander" principles both authors advocate, giving concrete mechanics for debating options rather than asserting authority. (InfoQ, "Decentralizing Architectural Decisions with the Architecture Advice Process," 2026, https://www.infoq.com/news/2026/03/architecture-advice-process/)

- **Practice guidance is stable.** The seven-step journey, change-driven process, decision-making heuristics, vision papers, change proposals/backlog, and templates-as-checklists are unchanged from the 2024 books; the 2024-2026 development is strong market validation of "start simple," AI changing the work unit, and decentralized decision processes operationalizing their leadership principles.
