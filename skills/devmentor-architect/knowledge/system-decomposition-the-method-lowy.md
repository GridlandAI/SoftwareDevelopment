# System Decomposition - "The Method" (Lowy)

**Domain:** 04 - Software Architecture Fundamentals
**Primary source:** Righting Software (Juval Lowy; 2019, Addison-Wesley)

## Overview
"The Method" is Juval Lowy's prescriptive approach to designing software systems and the projects that build them. It rests on one central claim: decompose a system by **encapsulating volatility**, not by decomposing functionality. Functional (or domain) decomposition - breaking a system into components named after the features or business nouns it performs - is, in Lowy's view, the root cause of most software-project failure, because functionality is what changes, so a functional decomposition shatters under change. Volatility-based decomposition instead identifies the *areas of change* and hides each behind a component, so that future changes are contained.

The Method matters across the whole SDLC because it is two disciplines, designed back-to-back as one continuous effort: **system design** (the architecture - what components exist and how they interact) and **project design** (how to build the system - duration, cost, staffing, risk, and a set of options for management). System design should take days to a week even for a large system; project design adds a few more days and turns "when will it be done and what will it cost" from guesswork into engineering. Lowy frames doing both with integrity as the defining mark of a professional: *treat the company's money as your own.*

## Core concepts

**Volatility-based decomposition.** Identify areas of volatility - things that will change across customers and over time - and encapsulate each in a component. A good test for a candidate volatility: can you state clearly *what* is volatile, *why* it is volatile, and *what risk* (likelihood × effect) it poses? If not, look further. Variability is not volatility: adding attributes to an entity (a tradesman's skills) is variation, not an axis of change deserving its own component.

**The nature of the business hardly ever changes.** Atomic business operations (a bank does credit and debit "since the time of the Medici") are stable; *how* and *in what sequence* you perform them is what changes. Architecture must be correct for the present and the far future, "as long as the nature of the business does not change."

**Required behavior, not required functionality.** Capture requirements as **use cases** - sequences of activities expressing how the system goes about its work - preferably as **activity diagrams** (which can express concurrency, blocking, and timing). Rule of thumb: any use case containing a nested "if" should be drawn, not written. A **core use case** captures the essence of the business; design need only be *validated* against the core use cases, though a good design will support all of them.

**Design iteratively, build incrementally.** You converge on a design through iteration, but you build a system the way you build a house or a car - incrementally, foundation up - because intermediate iterations (a motorcycle on the way to a car) have no business value and rework is wasteful.

**Closed architecture.** Components call only *down* one layer and never *up* or *sideways*; this trades flexibility for encapsulation and decoupling, which is the better trade for business systems. (Open architecture allows any call and sacrifices encapsulation; semi-closed/semi-open allows multi-layer-down calls, justified only for performance-critical or near-immutable code like a network stack.)

**Composable design.** A correctly decomposed system supports current and *future* requirements by recombining the same small set of components - without changing the components or the architecture. The TradeMe case study demonstrates supporting unforeseen use cases (e.g., "analyze all projects 2016–2019") with no design change.

## Frameworks & models

### The layered structure (the system-design template)
The Method prescribes four layers plus a vertical Utilities bar, loosely answering "who/what/how/where":

- **Clients** (the "who") - the top layer (not just "presentation"). End-user apps *and* other systems are all clients, using the same entry points and security. Encapsulates client volatility (desktop, web, mobile, API, admin). Most volatile layer; least reusable.
- **Managers** (the "what") - encapsulate volatility in the **sequence/orchestration** of a use case. A Manager owns a *family* of logically related use cases (often a subsystem). A good Manager is **almost expendable**: it only orchestrates Engines and ResourceAccess; if a change request makes you think (not shrug, not fight), it's well-sized.
- **Engines** (the "how") - encapsulate volatility in an **activity** (a business rule or computation). Essentially the Strategy pattern. Designed for reuse; shared across Managers. Far fewer than expected - an Engine requires genuine operational volatility (an unknown number of ways of doing something).
- **ResourceAccess** (the "how to reach state") - encapsulate volatility in *accessing* a resource *and in the resource itself*. The contract must expose **atomic business verbs** (e.g., `pay`, `credit`), never CRUD/IO verbs (`Select`, `Open`, `Read`) that leak the underlying resource. Highly reusable.
- **Resources** (the "where") - the actual physical stores (database, file, cache, queue), possibly whole external systems. Least volatile, most reusable.
- **Utilities bar** - cross-cutting services (security, logging, diagnostics, pub/sub, message bus, hosting) usable by any layer. Litmus test: could a smart cappuccino machine plausibly use it? (Security yes; a mortgage calculator no.)

**Key observations of a good design:** volatility *decreases* top-down (lower layers, which more things depend on, change least); reuse *increases* top-down; the design is **symmetric** (the same call patterns recur across use cases - asymmetry, by presence or absence, is a smell).

### The Method's guidelines and "don'ts"
- Naming: two-part PascalCase, suffix = type (`Manager`/`Engine`/`Access`). Gerunds belong only to Engine prefixes (`CalculatingEngine`); `BillingManager` smells of functional decomposition.
- Manager:Engine ratio tends to a "golden ratio" (≈2 Managers→1 Engine, 5→3); eight Managers means you have already failed (functional/domain decomposition). Limit Managers per subsystem to ~3.
- Closed-architecture relaxations: Managers may call Engines (not truly sideways - Engines are an orthogonal plane); a Manager may *queue* a call to another Manager (technically a down-call via a queue Resource); Utilities are callable from anywhere.
- Don'ts (each signals lurking functional decomposition): Clients don't call multiple Managers in one use case; Clients don't call Engines; Engines/ResourceAccess don't receive queued calls; Engines never call Engines; ResourceAccess never calls ResourceAccess; only Managers (and Clients) publish/subscribe to events; Resources don't publish events.

### Business alignment (driving the architecture from the business)
Vision → Objectives → Mission Statement → Architecture, with bidirectional traceability.
- **Vision** - one terse sentence read like a legal statement ("A platform for building applications to support the TradeMe marketplace").
- **Objectives** - business-only (not technology), each serving the vision (e.g., quick turnaround for new requirements; customization across markets; visibility/accountability; forward-looking; integration; security).
- **Mission statement** - *how*: "Design and build a collection of software components the team can assemble into applications and features." Because the mission is to build *components*, the business itself mandates volatility-based decomposition - reversing the usual dynamic where the architect pleads against functional decomposition.

### Identifying areas of volatility (the decomposition workflow)
1. Compile a glossary from who/what/how/where to align everyone.
2. Use the "what" list to *seed* candidate subsystems/swim lanes (a starting point, not the answer - only volatile "whats" earn a component).
3. Challenge each candidate (state what/why/risk). Reject domain/functional candidates (a `ProjectManager` or `TradesmanManager` betrays domain decomposition; the real volatility is the *marketplace* and *membership*).
4. Map volatilities to components - *not* 1:1 (one Manager can own several related volatilities). Volatilities outside the business scope become Resources (e.g., payment systems).
5. Reject **speculative design** (encapsulating volatility the business is not in the business of, e.g., project optimization) - fold it into an existing Manager until a real need appears.
6. Optionally use an **anti-design effort** (deliberately design the worst monolith/functional decomposition) to expose, by contrast, why volatility-based decomposition is better.

### Operational patterns (composition)
- **Validate the design** by drawing the call chain / sequence diagram for each (core) use case; if you cannot validate it, redesign.
- **Message bus** - a queued pub/sub (N:M) decoupling all Clients and Managers; provides availability, throughput, offline work. Justified only when extensibility is a top objective and the organization can absorb the complexity.
- **"The message is the application"** - there is no central "application"; the system is a loose set of services transforming messages on the bus, where required behavior is the aggregate of transformations. Maximizes decoupling and extensibility; aligns toward the actor model.
- **Workflow Managers** - Managers that store/load/execute workflows (often via a third-party workflow tool), enabling long-running workflows, fast feature turnaround, and even product-owner-edited behavior. Use only when workflow volatility justifies the added complexity.
- **Calibrate to the team.** "It is easier to morph the architecture than to bend the organization." A simpler design (Clients queue calls to Managers) may fit a less mature team better than a full message-bus design.

## The Method's project design

System design tells you *what* to build; project design tells you *how to build it* - duration, cost, staffing distribution, risk, and a set of options. Project design is to project management what architecture is to programming. Combining system + project design produces a non-linear improvement in the chance of success.

**Critical path method (CPM) on a project network.** The project is a network of **activities** (anything needing time and a resource) and their **dependencies** (including resource dependencies). Lowy prefers **arrow diagrams** over node diagrams because well-designed systems have repeated dependencies across layers (e.g., several Managers/Engines each using the same three ResourceAccess services) that turn node diagrams into an unreadable "cat's cradle"; arrow diagrams stay clear and make milestones natural. **Dummy activities** (zero duration) express dependencies that can't be drawn directly. The **critical path** sets the minimum project duration.

**Floats (safety margins).**
- **Total float** - how long an activity (or chain) can slip without delaying the project.
- **Free float** - how long it can slip without disturbing *any* other activity.
- Float visualization via color coding (red/yellow/green) using relative, exponential, or absolute criticality. Low float = high risk. *Float-based scheduling*: assign resources to the lowest-float activity first (safest and most efficient); you can *trade float for resources* (fewer developers) and thereby *trade cost for risk* - a three-way trade among time, cost, and risk.
- Well-managed projects usually slip not on the critical path (watched like a hawk) but because **non-critical activities bleed out their float and become critical**; track chain floats proactively.

**Time, cost, and the time–cost curve.** Compress the critical path by redesigning the project into progressively shorter solutions, producing a time–cost curve. Beyond the normal solution, each compression buys schedule at rising cost.

**Risk as the third dimension.** Every option is a point in (time, cost, risk) space. People choose by risk, not expected value (Prospect Theory - Kahneman & Tversky). The **time–risk curve** is a logistic function with a tipping point, not a smooth line. Model and quantify risk; find the **risk exclusion zone** (too risky) and the acceptable zone; look for the *behavior* (the tipping point) rather than fixating on specific values (≈0.5 decompression target, ≈0.3 minimum risk are starting heuristics).

**Options and "Optionality."** There is no "the" project - always present management with several viable options (each a time/cost/risk blend). Three options is ideal; two is too few; five is too many (the Paradox of Choice). Communicating in options empowers decision-makers and is the language of management.

**Project-design solutions to explore:** the **normal** solution (efficient team on the critical path), **limited-resources** solution, **subcritical** solution, and compression via top resources, parallel work, activity changes, and to minimum duration; plus **decompression** of the normal solution to reduce risk.

## Patterns

**Volatility-based decomposition** - *intent:* contain change by encapsulating each area of volatility in a component. *When:* any system expected to evolve (i.e., nearly all). *Trade-offs:* requires skill and discipline and feels less "natural" than functional decomposition, but yields maintainable, reusable, extensible, composable systems.

**Closed layered architecture (Clients/Managers/Engines/ResourceAccess/Resources + Utilities)** - *intent:* a universal template that encapsulates the common areas of volatility with prescribed interactions. *When:* most business systems. *Trade-offs:* maximum decoupling/encapsulation at the cost of flexibility and some indirection; relax the rules judiciously.

**Message bus / "the message is the application"** - *intent:* decouple all clients and managers via queued pub/sub so behavior is the flow of messages. *When:* extensibility is a primary business objective and the organization is mature enough. *Trade-offs:* extreme decoupling and forward-compatibility vs. significant added complexity, new APIs, intricate failure modes.

**Workflow Manager** - *intent:* externalize volatile orchestration into editable, persistable workflows. *When:* high sequence volatility and need for rapid feature turnaround. *Trade-offs:* fast change and possible business-user authoring vs. tooling dependency and learning curve.

**Critical-path project design with options** - *intent:* engineer the build (duration/cost/risk) and offer management a small set of options. *When:* always (ROI is favorable since it costs days). *Trade-offs:* upfront design effort vs. realistic commitments and the ability to objectively answer change requests.

## Principles & best practices
- Decompose by volatility; the nature of the business is the stable bedrock.
- Capture required *behavior* via use cases / activity diagrams; design against *core* use cases.
- Volatility decreases and reuse increases top-down; strive for symmetry.
- Keep Managers almost-expendable; keep Engines few; expose atomic business verbs in ResourceAccess.
- Prefer closed architecture; relax rules only with justification.
- Design iteratively, build incrementally; never build the system iteratively.
- Drive the architecture from Vision → Objectives → Mission → Architecture with full traceability.
- Always pair system design with project design, back-to-back, as one effort.
- Reserve float; manage it actively; trade among time, cost, and risk consciously.
- Present 3 options; communicate in Optionality; don't exceed ~30% compression.
- "Design a sun dial, not a clock" - project design need only be accurate enough to support decisions; don't chase false precision.
- Architecture (not estimation) sets project duration via network topology; decent estimates and decent developers average out.
- The real answer to "when to design a project" is **when you have integrity** - treat the company's money as your own.

## Anti-patterns & pitfalls
- **Functional / domain decomposition** - components named after features or business nouns; produces tight coupling, expensive Managers, Clients that stitch functionality together, and chained sideways/up calls. The single biggest failure mode; Lowy fears microservices done this way will be "the biggest failure in the history of software."
- **Microservices as subsystem-level functional decomposition** + REST/HTTP everywhere - using public-facing protocols internally; internal services should use fast, reliable channels (TCP, named pipes, IPC, queues).
- **Expendable (pass-through) Managers** and **too many Engines/Managers** - design distortions signaling missed or absent volatility, or functional decomposition.
- **CRUD-like ResourceAccess contracts** that leak the resource and force system-wide change when the resource changes.
- **Speculative design** - encapsulating volatility the business doesn't care about.
- **Open/sideways/up calls** - importing volatility upward, coupling within layers; almost always functional decomposition.
- **Designing a project without a solid (volatility-encapsulating) architecture** - the design will change, voiding the project design.
- **Two-dimensional thinking (time + cost)** for a three-dimensional (time, cost, risk) problem; ignoring risk because it seems unmeasurable.
- **Trading away all float** for cost, creating a fragile low-float project.
- **Compressing blindly with top resources** - can create a new critical path staffed by the worst developers, idle heroes, and reduced efficiency.

## Decision guidance
- **Decompose:** list who/what/how/where; seed subsystems from "what"; for each candidate, prove volatility (what/why/risk) or discard it; map volatilities (not 1:1) to Clients/Managers/Engines/ResourceAccess/Resources/Utilities; reject speculative and domain-flavored candidates.
- **Choose interactions:** default to closed architecture; use a message bus only if extensibility is a stated objective and the org is mature; use workflow Managers only if sequence volatility is high.
- **Validate:** draw a call chain per core use case; check for symmetry; an unintelligible or asymmetric design is wrong.
- **Design the project:** gather core use cases → system design (call chains + component list) → list non-coding activities → estimate → normal solution → explore limited-resource/subcritical → compress (top resources, parallel, activity change, minimum) → time–cost curve → decompress for risk → model risk → find exclusion/acceptable zones → identify viable options → SDP review.
- **Choose an option by risk first**, then time and cost; present three; look for the risk tipping point, not a magic number.
- **When to design a project:** essentially always (favorable ROI), and especially for large projects, aggressive deadlines, and whenever you are personally accountable. The differentiator that advances careers is bundling system design with project design and refusing anything less.

## Source attribution
- **The Method overview, functional vs. volatility-based decomposition, business-as-stable:** Righting Software (Lowy 2019), ch. 1–2.
- **Structure - layers, Managers/Engines/ResourceAccess/Resources/Utilities, naming, ratios, closed/open architecture, don'ts, symmetry, microservices critique:** ch. 3.
- **Composition and design validation:** ch. 4.
- **System design example (TradeMe): vision/objectives/mission, identifying volatility, static architecture, message bus, "message is the application," workflow Managers, use-case validation:** ch. 5.
- **Project design: motivation, network & float, time & cost, risk, options, advanced techniques, when/why to design a project, integrity:** ch. 6–14.
- **Service contract design and granularity (atomic business verbs, area of minimum cost):** Appendix B.

## Recent developments (2024–2026)

- **The microservices-consolidation trend echoes Lowy's central warning.** Lowy's fear that microservices built as subsystem-level *functional* decomposition would be "the biggest failure in the history of software" found broad empirical support in 2025: reporting cited ~42% of organizations consolidating microservices back into larger units, citing the debugging and operational pain that functional, chatty service boundaries produce. The Method's volatility-based, closed-architecture stance is one principled alternative to the distributed-big-ball-of-mud outcome teams are now reversing. (SoftwareSeni, "From Microservices Consolidation to Modular Monoliths," 2025, https://www.softwareseni.com/understanding-modern-software-architecture-from-microservices-consolidation-to-modular-monoliths/)

- **AI-assisted decomposition contrasts sharply with The Method.** The dominant 2025-2026 decomposition research and tooling use LLMs and graph clustering to partition systems largely along *structural/functional* lines (dependency graphs, code similarity), the opposite of Lowy's "encapsulate volatility, not functionality" principle. This sharpens the debate: automated tools optimize for coupling metrics, whereas The Method optimizes for containing future change, a distinction AI decomposition does not currently capture. (Springer, "A Pattern-Driven and LLM-Assisted Approach for Decomposing Monolithic Systems into Microservices," 2025, https://link.springer.com/chapter/10.1007/978-981-95-5012-8_16)

- **Uncertainty-first methods offer a parallel "design for change" lens.** Barry O'Reilly's Residuality Theory (2024, widely taught 2025-2026) shares The Method's premise that architecture must survive change, but reaches it via random stressor simulation and complexity science rather than enumerated volatilities and a prescriptive layered template, giving practitioners a complementary vocabulary for the same goal. (InfoQ, "Producing a Better Software Architecture with Residuality Theory," 2025, https://www.infoq.com/news/2025/10/architectures-residuality-theory/)

- **The Method itself is unchanged.** *Righting Software* (2019) has had no new edition; volatility-based decomposition, the Clients/Managers/Engines/ResourceAccess/Resources layers, and CPM-based project design stand as published. The 2024-2026 relevance is contextual: industry pain with functional microservices validates Lowy's critique, while AI decomposition and residuality represent contrasting approaches to the same "design for change" problem.
