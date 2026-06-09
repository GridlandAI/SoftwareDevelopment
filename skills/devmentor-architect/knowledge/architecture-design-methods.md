# Architecture Design Methods

**Domain:** 04 - Software Architecture Fundamentals
**Primary sources:** Designing Software Architectures: A Practical Approach, 2nd ed. (Cervantes & Kazman, 2024); Hands-on Software Architecture: Unified Architecture Process (Kim & Kim, 2025); Just Enough Software Architecture (Fairbanks, 2010); Software Architecture and Design: The Practical Guide (Köhler, 2025)

## Overview
Architecture design methods give architects a repeatable way to turn requirements and quality goals into structures, rather than relying on intuition or letting an architecture "emerge." They matter because architectural decisions are early, far-reaching, and hard to change: they fix the work-breakdown structure, the technologies, the skills needed, and the system's ability to meet quality attributes. A good method tames complexity by working at multiple levels of abstraction, by reusing proven design concepts (reference architectures, patterns, tactics, frameworks) instead of reinventing solutions, and by interleaving documentation and analysis with design so the result can be reasoned about and evaluated.

This file covers two complementary step-by-step methods - Attribute-Driven Design (ADD) from Cervantes & Kazman and the Unified Architecture Process (UAP) from Kim & Kim - together with the supporting machinery both rely on: quality attribute scenarios, the Quality Attribute Workshop (QAW), the design-concepts catalog (tactics, patterns, externally developed components), and evaluation methods such as ATAM and CBAM.

## Core concepts

- **Architectural driver.** Any factor that shapes architectural decisions. Cervantes & Kazman group drivers into five categories: design purpose, quality attribute requirements, primary functional requirements, architectural concerns, and constraints. Drivers must be baselined and managed across the life cycle.
- **Design purpose.** Why and when the design is being done: for early estimation/proposal, for an exploratory prototype, or during development of a new/existing system. The purpose shapes both the process and the outputs.
- **Quality attribute.** A measurable or testable property of a system (performance, availability, modifiability, security, etc.) that indicates how well the system satisfies stakeholder needs. Among the drivers, quality attributes shape the architecture the most.
- **Quality attribute scenario.** The preferred way to express a quality requirement unambiguously. A complete scenario has six parts: source of stimulus, stimulus, artifact, environment, response, and response measure. Example: "When the user presses play (stimulus, source = user) during normal operation (environment), the system (artifact) begins streaming (response) in under 5 seconds (response measure)." Scenarios are testable, falsifiable hypotheses.
- **Primary functionality.** Functionality critical to the business goals, typically high business value, high technical difficulty, or requiring many elements. As a rule of thumb roughly 10 percent of use cases/user stories are primary. Allocation of functionality to elements (not the functionality itself) is what matters architecturally.
- **Architectural concern.** Considerations that drive design but are not traditional requirements: general concerns (overall structure, allocation of modules to teams, code-base organization), specific concerns (logging, caching, authentication, exception management), internal/derived requirements (e.g., store all timestamps in UTC), and issues surfaced by reviews.
- **Constraint.** A decision over which the architect has little or no control (mandated technologies, laws, deadlines, backward compatibility). The architect's job is to satisfice - design the best system possible within the constraints.
- **Design concept.** A reusable building block for creating structures: reference architectures, patterns, tactics, and externally developed components (see catalog below).
- **Structure / view.** Architecture is a set of structures grouped as module structures (static: files, modules, classes), component-and-connector (C&C) structures (runtime: processes, threads), and allocation structures (mapping software to hardware, teams, file systems). A view is a representation of a structure.

## Frameworks & models

### Attribute-Driven Design (ADD), version 3.0 (Cervantes & Kazman)
ADD is a step-by-step method performed in a series of design rounds (one round can map to a sprint), each round running several design iterations. The seven steps (with significant micro-iteration between steps 3–5):

1. **Review inputs.** Confirm the design purpose, and that primary functional requirements, prioritized quality attribute scenarios, constraints, and architectural concerns are available and correct. For existing/non-greenfield work, also bring in the existing architecture. Drivers form the architectural design backlog. ADD is "garbage in, garbage out" - getting drivers and their priorities right is crucial.
2. **Establish the iteration goal by selecting drivers.** Pick the subset of drivers this iteration will address (a single important driver, a family of similar drivers, or a set of related drivers). For greenfield mature-domain systems the first goal is usually to establish an overall structure via a reference architecture.
3. **Choose one or more elements to refine.** Refinement means decomposition (top-down), aggregation (bottom-up), or improvement of previously identified elements. Greenfield starts by selecting the system itself for decomposition; brownfield requires understanding the as-built architecture first.
4. **Choose one or more design concepts that satisfy the selected drivers.** The hardest step: identify candidate concepts (reference architectures, patterns, tactics, frameworks) and select among them. Often several concept types are combined for one driver.
5. **Instantiate architectural elements, allocate responsibilities, and define interfaces.** Adapt the generic design concept to the problem (e.g., decide how many layers), allocate responsibilities respecting high cohesion / low coupling, connect elements, and specify interfaces (contractual operations, parameters, return values, exceptions, QoS).
6. **Sketch views and record design decisions.** Capture preliminary views (sketches, not yet formal documentation) plus the significant decisions and their rationale (tradeoffs, assumptions, evidence).
7. **Perform analysis of current design and review iteration goal and design purpose.** Self-review or, better, have someone else review; decide whether enough iterations have been done to satisfy the round's drivers and the design purpose.

Then **iterate if necessary** - risk is the guide for whether more iterations are needed; at minimum address the highest-priority drivers.

**Applying ADD to different contexts.** (1) Greenfield in a mature domain: follow the design-concept selection roadmap - first establish overall structure (reference architecture + deployment patterns + full-stack frameworks), then identify structures supporting primary functionality, then refine to address remaining drivers. (2) Greenfield in a novel domain: fewer reference architectures exist, so rely on general tactics/patterns plus strategic prototyping. (3) Brownfield/existing: first understand the existing architecture, then refine structures for new drivers. (4) Replacing legacy: use patterns like the Strangler Fig to substitute services gradually behind a proxy.

**Tracking progress.** ADD adds no native tracking, so use an architectural (design) backlog of "enablers" and a design Kanban board with columns Not Yet Addressed / Partially Addressed / Completely Addressed, with an explicit "definition of addressed."

### The Unified Architecture Process (UAP) (Kim & Kim)
UAP is a "cookbook" methodology that organizes design into a hierarchy of Activity → Step → Task, with detailed instructions and validation checklists per step. It is grounded in requirements engineering, software process models, design principles (abstraction, modularity, separation of concerns, SOLID), SQA, OOAD/UML, design patterns, component-based development, product-line engineering, service-oriented computing, and machine learning. Six major activities (A4 splits into four view sub-activities):

- **A1. Requirements Refinement.** Step 1 identify stakeholders (locate groups, create profiles); Step 2 refine functional requirements (find and resolve deficiencies); Step 3 refine non-functional requirements (acquire architectural concerns, find and resolve deficiencies).
- **A2. System Context Analysis.** Build a context model: Step 1 boundary context (tiers as processes, boundary elements as terminals, persistent data as data stores, information flows as data flows); Step 2 functional context (actors, use cases, use-case relationships); Step 3 information context (persistent classes, relationships, cardinality); Step 4 behavior context (allocate functional groups to tiers, invocation patterns, context-level control flow).
- **A3. Schematic Architecture Design.** Step 1 identify candidate architecture styles (observe structural characteristics, determine inherent system types, determine candidate styles); Step 2 evaluate candidates (applicable situations, benefits, drawbacks, applicability); Step 3 integrate styles with tiers/services/layers/behavior; Step 4 refine the schematic architecture.
- **A4. Design for Architecture Views.** A4a Functional view (refine use cases → define functional components → allocate → define interfaces → design for variability via the open-closed principle). A4b Information view (refine persistent object model → define data components → allocate → define interfaces → design object persistence). A4c Behavior view (refine control flows → identify key behavioral elements → design detailed control flows). A4d Deployment view (computing devices → execution environments → network connectivity → allocate software artifacts).
- **A5. Design for Non-Functional Requirements.** A dedicated, six-step activity: (1) identify underlying facts and policies behind each NFR; (2) define criteria for tactics; (3) define candidate tactics in technical detail; (4) evaluate candidate tactics via cost-benefit analysis and trade-off assessment, selecting a subset; (5) apply/integrate selected tactics into the views; (6) validate conformance to both functional and non-functional requirements.
- **A6. Architecture Evaluation.** Step 1 identify target elements to evaluate (styles, schematic architecture, view designs, allocation, deployment); Step 2 select evaluation methods; Step 3 apply them. Used for verification and validation before implementation.

UAP can be **tailored** - activities may be omitted depending on project context (e.g., A1 if requirements are already well-refined). It ships with a standardized architecture description template aligned 1:1 with the activities.

### Quality Attribute Workshop (QAW) (Cervantes & Kazman)
A facilitated, stakeholder-focused method to generate, prioritize, and refine quality attribute scenarios, ideally before the architecture is fixed. Eight steps: (1) QAW presentation and introductions; (2) business goals presentation; (3) architectural plan presentation; (4) identification of architectural drivers; (5) scenario brainstorming (each scenario gets explicit stimulus + response, traceable to drivers and business goals); (6) scenario consolidation; (7) scenario prioritization (each stakeholder gets votes equal to ~30% of the scenario count); (8) scenario refinement into six-part scenarios. Output: a prioritized, business-aligned scenario list. Runs from 2–3 hours up to 2 days. The **Mission Thread Workshop** serves the same purpose for systems of systems.

### Utility Tree (Cervantes & Kazman)
A lighter alternative when stakeholders are not available. Root "Utility" branches into quality attributes, then into refined concerns ("latency of price changes" rather than "performance"), with leaves expressed as concrete scenarios. Scenarios are prioritized on two dimensions - business importance (ranked by customer) and technical risk (ranked by architect) - using L/M/H. Focus on (H,H), then (H,M)/(M,H). QAW and the Utility Tree are complementary: QAW excels at external stakeholders, the Utility Tree at internal ones.

## Patterns
The following are *design concepts* (the catalog ADD draws on) plus key tactics groupings. (See PATTERN-CATALOG.md.)

**Reference architecture** - a blueprint giving overall logical structure for a class of applications, mapping a reference model onto one or more architectural patterns and shipping supporting artifacts. *When to use:* greenfield mature-domain systems, to establish overall structure early. *Trade-offs:* adopting it also imports a set of concerns (security, communication) you must then address; risk of poor fit if the domain differs.

**Architectural / design pattern** - a conceptual solution to a recurring design problem in a context; balances multiple competing forces (multiple quality goals). *When to use:* whenever a known, proven solution fits the problem. *Trade-offs:* every pattern resolves some forces at the expense of others.

**Tactic** - a design primitive that controls a *single* quality attribute response. "A tactic is an atom; a pattern is a molecule." Established tactics categorizations exist for availability, deployability, energy efficiency, interoperability, modifiability, performance, safety, security, testability, usability. *When to use:* top-down, to reason systematically about achieving one quality goal. *Trade-offs:* a tactic improving one quality usually degrades another.
- *Performance tactics:* Control Resource Demand (manage work requests, limit event response, prioritize events, reduce computational overhead, bound execution times, increase efficiency of resource usage) and Manage Resources (increase resources, introduce concurrency, maintain multiple copies of computations/data, bound queue sizes, schedule resources).
- *Availability tactics:* Detect Faults (monitor, ping/echo, heartbeat, timestamp, condition monitoring, sanity checking, voting, exception detection, self-test); Recover from Faults - Preparation & Repair (redundant spare, rollback, exception handling, software upgrade, retry, ignore faulty behavior, graceful degradation, reconfiguration) and Reintroduction (shadow, state resynchronization, escalating restart, non-stop forwarding); Prevent Faults (removal from service, transactions, predictive model, exception prevention, increase competence set).
- *Modifiability tactics:* Increase Cohesion (split module, redistribute responsibilities); Reduce Coupling (encapsulate, use an intermediary, abstract common services, restrict dependencies); Defer Binding (compile/build-time, deployment/startup-time, runtime).
- *Security tactics:* Detect Attacks, Resist Attacks, React to Attacks, Recover from Attacks (mapping confidentiality/integrity/availability - the CIA triad).

**Externally developed component** - a concrete technology not built in the project: technology families (e.g., RDBMS, ORM), products (e.g., PostgreSQL, external APIs, cloud capabilities), application frameworks (full-stack like Spring; specific like JPA/Hibernate), and platforms (PaaS). *When to use:* to make abstract tactics/patterns concrete (the "buy vs. build" decision). *Selection criteria:* problem addressed, cost, license type, vendor lock-in, learning curve, community support.

**Load Balancer** - intermediary that distributes client requests across a pool of (usually stateless) providers. Packages Increase resources / Maintain multiple copies of computations / Introduce concurrency. *Trade-offs:* the balancer itself can be a bottleneck or single point of failure (so it is often replicated).

**Throttling** - packages the Manage work requests tactic; a throttler limits access to a service to keep it in a performance sweet spot. *Trade-offs:* throttling logic must be fast; large buffers or lost requests if demand exceeds capacity; hard to retrofit.

**Redundancy spares (Hot / Warm / Cold)** - active, passive, and out-of-service standbys realizing the Redundant Spare tactic. *Trade-offs:* hot = fastest recovery, highest cost; cold = cheapest, highest MTTR.

**Circuit Breaker** - stops endless retries against a failing networked service, returning immediately until reset; prevents cascading failures. *Trade-offs:* timeout/retry values must be tuned (too long adds latency, too short trips unnecessarily).

**Strangler Fig** - gradually replaces a legacy system behind a proxy that progressively routes calls to new components. *When to use:* incremental legacy replacement. *Trade-offs:* proxy complexity; long migration period.

## Principles & best practices
- Don't start design without a prioritized list of measurable quality attribute scenarios; "box in" reluctant stakeholders to at least an order-of-magnitude response measure.
- Satisfice, don't optimize: search the design space until an acceptable solution is found, because every quality improvement trades off against another.
- Follow the nine decision-making principles (Cervantes & Kazman): use facts, check assumptions, explore contexts, anticipate risks, assign priorities, define a time horizon, generate multiple solution options, design around constraints, weigh pros and cons.
- "A good engineer makes a design that works with as few original ideas as possible" - identify and combine existing design concepts rather than reinventing.
- Record responsibilities and design decisions (with rationale) as you make them, not later.
- Interleave preliminary documentation and analysis with design; a clean separation of design/document/analyze phases is artificial and harmful.
- Make a dedicated, explicit activity for NFRs (UAP's A5) rather than deferring quality concerns to maintenance.

## Anti-patterns & pitfalls
- Treating quality requirements vaguely ("make it fast," "make it modifiable") instead of as six-part scenarios.
- Starting ADD with poorly formed drivers (garbage in, garbage out).
- Choosing the first design concept that comes to mind (anchoring bias); not generating alternatives.
- Selecting iteration goals that are too big (too many drivers) or too small (one trivial driver).
- Confusing identifying nonprimary modules early for estimation with Big Design Up Front (BDUF).
- Applying a standard documentation template or fixed set of diagrams regardless of the actual risks/drivers.
- Skipping analysis because "the architecture is done" - comprehensive evaluations such as ATAM fit poorly as a one-time milestone in iterative/agile contexts; analysis should be continuous.

## Decision guidance
- **Pick the method to the context.** Use ADD when you want lightweight, iteration-friendly, driver-and-risk guidance that plugs into Agile/Scrum via a design backlog and Kanban. Use UAP when you need exhaustive, cookbook-level Activity/Step/Task instructions, checklists, and a documentation template for large, complex, or regulated systems (and tailor out activities you do not need).
- **Sequence the work.** For greenfield mature domains, establish overall structure first (reference architecture + deployment patterns + full-stack framework), then allocate primary functionality, then refine for remaining quality drivers. For novel domains, lean on tactics/patterns plus prototyping.
- **Choosing a design concept (ADD step 4 / UAP A5).** Tabulate alternatives with pros/cons against the drivers; for economically significant decisions use CBAM (utility–response curves, ROI) or SWOT; when information is lacking or technology is new, build throwaway prototypes (cost ratio ~5–10× analysis but worth it for emerging/risky tech).
- **Tactic vs. pattern.** Reach for a tactic to control a single quality response; reach for a pattern when balancing several forces at once. Combine concept types freely (e.g., a security pattern + security tactic + security framework).
- **When to evaluate and how.** Match analysis cost to risk: thoughtful questions and tactics-based questionnaires are cheap; prototypes, simulations, and formal models cost more but raise confidence. Use ATAM/SAAM for scenario-based evaluation of risks and tradeoffs; use CBAM to weigh economic value; use PoC/prototype-based evaluation when feasibility is uncertain (UAP's five approaches: scenario-, model-, formal-method-, PoC-, and prototype-based).

### Evaluation methods at a glance
- **ATAM (Architecture Tradeoff Analysis Method).** Scenario-driven evaluation by an evaluation team, decision makers, and stakeholders. Nine steps across two analysis phases (present ATAM; present business drivers; present architecture; identify architectural approaches; generate quality attribute utility tree; analyze approaches; brainstorm and prioritize scenarios; analyze approaches; present results). Outputs: prioritized scenarios, mapping of decisions to qualities, sensitivity and tradeoff points, risks/non-risks, and risk themes. Typically two two-day meetings; identifies risks, not precise numbers.
- **CBAM (Cost Benefit Analysis Method).** Quantitative selection among architectural strategies via utility–response curves (worst/best/current/desired levels), expected responses, costs, and ROI. Can be used during design or after ATAM.
- **SAAM / PATS.** Other scenario-based methods (Software Architecture Analysis Method; Performance Analysis Through Scenarios).
- **Tactics-based analysis.** Turn each tactic in a categorization into an interview/questionnaire question to rapidly map what the architecture does and does not do (≈30–90 min per quality attribute).
- **ARID (Active Reviews for Intermediate Design).** Reviewers exercise a partial design against scenarios (often writing pseudo-code) to identify interfaces and validate intermediate design.

## Source attribution
- ADD steps, design-concept catalog, tactics categorizations, QAW, Utility Tree, CBAM, ATAM, ARID, tactics-based analysis, design backlog/Kanban, six-part scenarios, decision-making principles: Designing Software Architectures, 2nd ed., chapters 2–4 and 11 (Cervantes & Kazman, 2024).
- UAP Activity/Step/Task process model (A1–A6), A5 NFR design steps, A6 evaluation, the five evaluation approaches, tailoring, underlying technologies: Hands-on Software Architecture, chapters 2, 10, 11, 13 (Kim & Kim, 2025).
- Risk-driven framing of "how much architecture is enough," architecture-vs-detailed-design, QAW reference for risk elicitation: Just Enough Software Architecture, chapters 2–3 (Fairbanks, 2010).
- arc42 mapping of quality goals/quality tree to documentation, C4, architect responsibilities: Software Architecture and Design, chapters 1, 3, 5 (Köhler, 2025).

## Recent developments (2024–2026)

- **LLM-assisted ADD.** A 2025 arXiv paper proposes an LLM-assisted approach to performing Attribute-Driven Design, using a model to help select design concepts and instantiate elements against prioritized drivers. This targets ADD's hardest step (step 4, choosing design concepts) while keeping the architect responsible for the trade-off judgments, an early sign that the step-by-step methods are becoming AI-augmented rather than replaced. (arXiv, "An LLM-assisted approach to designing software architectures using ADD," 2025, https://arxiv.org/pdf/2506.22688)

- **AI support for ATAM evaluation.** Companion 2025 research uses generative AI to partially automate ATAM scenario evaluation: suggesting the most suitable quality-attribute scenarios, flagging risks, surfacing trade-offs, and checking whether the selected scenarios adequately cover the architecture. This directly addresses this file's caution that comprehensive ATAM fits poorly as a one-time milestone, by making lightweight, continuous scenario analysis cheaper. (arXiv, "Supporting architecture evaluation for ATAM scenarios with LLMs," 2025, https://arxiv.org/html/2506.00150v1)

- **Evaluation methods extended to AI-agent architectures.** New methods such as AgentArcEval adapt scenario-based architecture evaluation to foundation-model-based agent systems, signaling that the ADD/ATAM/quality-scenario machinery is being generalized to evaluate AI agents as architectural elements. (arXiv, "AgentArcEval: An Architecture Evaluation Method for Foundation Model based Agents," 2025, https://arxiv.org/html/2510.21031v1)

- **Methods themselves are stable.** ADD 3.0, UAP, QAW, utility trees, the design-concept catalog (tactics/patterns/frameworks), ATAM/CBAM/ARID, and the six-part quality scenario are unchanged from the 2024-2025 books; the 2024-2026 development is AI assistance for the labor-intensive steps (concept selection, scenario evaluation) and extension of these methods to AI-agent systems.
