# iSAQB Architecture Fundamentals (CPSA Foundation Body of Knowledge)

**Domain:** 04 - Software Architecture Fundamentals
**Primary sources:** Software Architecture Fundamentals (Gharbi, Koschel, Rausch; 2019, Rocky Nook); Software Architecture Fundamentals - iSAQB-Compliant Study Guide, 2nd ed. (Gharbi, Koschel, Rausch, Tiemeyer; 2024)

## Overview
This topic captures the body of knowledge taught for the iSAQB Certified Professional for Software Architecture - Foundation Level (CPSA-F). It is the shared vocabulary and method that lets an architect reason about *any* software-intensive system: what architecture is, what the architect does, what forces shape a design, how to design and describe it with views and cross-cutting concepts, and how to evaluate it. It matters in the SDLC because architecture is the "framework for change": it fixes the load-bearing structures of a system early, when changing them is cheap, and governs the quality attributes (maintainability, performance, security, and the rest) that determine whether a system can survive years of evolving requirements.

The iSAQB approach is deliberately pragmatic and technology-neutral. It does not prescribe one notation or one method; it teaches a small set of durable concepts (building blocks, interfaces, views, quality scenarios) and lets the architect calibrate rigor to the risk and complexity of the system at hand.

## Core concepts

**Software-intensive system.** A collection of building blocks organized so that, together, they accomplish the system's purpose, where software building blocks perform the essential tasks. Software here means programs *plus* procedures, data, and documentation (IEEE 610.12). Three broad categories each suggest different default architectures: information systems (usually layered, transactional, data-heavy), embedded systems (resource-constrained, loosely coupled active processes communicating over buses), and mobile systems (semi-autonomous, high-interaction, intermittent connectivity).

**Software architecture (working definition).** The fundamental principles and rules for organizing a system into building blocks and interfaces, their relationships to each other and to the environment, and the guidelines governing the whole lifecycle from analysis through operation and enhancement. Anchored in ISO/IEC/IEEE 42010. Two aspects: a *constructive* aspect (the structure) and a *procedural* aspect (the rules and principles developers must follow). Every system has an architecture whether or not it was designed; the question is only whether it was designed deliberately.

**Building block.** The central static element of an architecture - an abstraction of source-code artifacts at any scale (function, class, package, library, subsystem, layer, framework). Three defining characteristics: (1) provided and required interfaces guaranteed as a contract; (2) encapsulation and interchangeability (replaceable by another block offering and requiring the same interfaces); (3) configuration and hierarchical (de)composition (a block can be built from sub-blocks).

**Interface.** A well-defined access point to a system or building block, specifying as precisely as necessary the syntax, data structures, functional and error behavior, non-functional characteristics, protocols, and constraints. Complete interface descriptions are usually impossible; the architect decides which aspects must be specified. Interfaces may be standard, provider-defined, consumer-defined (common in frameworks), or independent (maximum decoupling via an adapter, at higher cost).

**Views (black/gray/white box).** Black box exposes only provided/required interfaces (the user's view, honoring information hiding). Gray box adds technical/runtime interfaces. White box (glass box) reveals internal decomposition into sub-blocks (the implementer's view). Hierarchical refinement walks from black box down through white boxes.

**Architecture description, viewpoints, views (42010).** A system has stakeholders; stakeholders have concerns; the architecture description (a *collection* of artifacts) addresses those concerns through views, each governed by a viewpoint (conventions, notations, model kinds, analysis techniques for one type of view). The architecture description records rationale linking decisions back to concerns.

**Architectural levels.** Descriptions are organized along two dimensions: perspective (functional/application "A-architecture" vs. technical "T-architecture", plus architectural style and technical infrastructure) and degree of abstraction (stepwise refinement). The architectural style is the central metaphor ("a three-layer architecture with MVC in the presentation layer and ORM for persistence"); the technical infrastructure defines network/deployment profiles.

**Quality (ISO/IEC 25010).** Architecture quality is judged against explicit quality requirements derived from objectives, constraints, and requirements (including implicit ones, which must be surfaced). The eight top-level characteristics: functional suitability, reliability, usability, performance efficiency, security, compatibility, maintainability, portability. Quality attributes interact and conflict (security can reduce usability; flexibility can reduce performance and testability), so the architect must balance and prioritize per system.

## Frameworks & models

**The Twin Peaks model.** Requirements engineering and architecture design iterate together. An initial high-level architecture lets you assess the real cost of requirements (often causing stakeholders to drop some); existing design alternatives expose under-specified requirements. Neither is a one-way street.

**The four design activities (iterative, incremental, top-down/bottom-up).** Architecture design is not sequential. The architect continuously moves between four equally weighted activities in the order the project needs: (1) analyze requirements and constraints; (2) develop architecture views and technical/cross-cutting concepts; (3) evaluate architecture and design decisions; (4) support and review the implementation. Simultaneously the architect moves between abstraction levels (requirements/constraints → architecture style & infrastructure → functional & technical levels → program design & implementation).

**The four influencing areas (environment).** Architecture is shaped by, and shapes, four neighboring areas: project environment & project management; product management & requirements engineering; execution platform & operations; tools & development environment. These supply requirements and constraints and receive feedback (e.g., a simpler architecture in exchange for a relaxed requirement).

**The 4+1 / arc42 view set.** The iSAQB endorses four proven views (derived from Kruchten's 4+1 and the arc42 template):
- **Context view** - system as black box; all external actors (systems, users) and every interface (type, data, protocol, sync/async pattern). The bridge from requirements to architecture.
- **Building block view** - static structure; UML components/packages, refined hierarchically (black box → white box). Each block: name, responsibility, interface, implementation reference.
- **Runtime view** - dynamic interactions of key scenarios (sequence, activity, communication, BPMN diagrams). Focuses on important sequences, not the whole system.
- **Deployment / infrastructure view** - mapping of software artifacts to physical nodes, networks, runtime environments (UML deployment diagrams).

**ISO/IEC 25010 quality model.** Used both to specify quality requirements and as a checklist for completeness (cross-checked with FURPS). Scalability is a common added attribute (vertical = bigger machine; horizontal = more machines).

**Architecture frameworks (awareness level).** TOGAF, RM-ODP, the Zachman Framework, MDA. All share the same two-dimensional structure (perspective × abstraction); recognizing this makes any of them navigable.

## Patterns

**Layered architecture** - *intent:* structure complex systems as horizontal layers of equal abstraction, each using only the layer below. *When:* information systems, where reuse/portability matter. *Trade-offs:* reduces dependencies and increases reuse, but adds pass-through overhead and vertical change ripple; skipping layers for performance reintroduces coupling.

**Pipes and filters** - *intent:* chain processing units (filters) connected by data channels (pipes). *When:* compilers, signal/stream processing, staged transformations. *Trade-offs:* high reusability and recombination; poor at handling error states mid-pipeline.

**Blackboard** - *intent:* independent knowledge sources contribute partial solutions to a shared blackboard under a control component. *When:* approximate/incremental problems - image and speech recognition, monitoring. *Trade-offs:* flexible for ill-structured problems; control logic and coordination are complex.

**Broker** - *intent:* a switching center decouples distributed clients and servers; servers register services, clients call through the broker (location transparency). *When:* distributed systems with changing topology. *Trade-offs:* transparency and dynamic binding at the cost of an extra hop and broker dependency.

**Service-oriented architecture (SOA)** - *intent:* expose functional interfaces as distributed, loosely coupled, reusable services via provider/directory/consumer roles. *When:* coarse-grained, reusable, location-independent capabilities. *Trade-offs:* ideally stateless, idempotent, self-contained; contract stability is essential; coarse granularity.

**Microservices** - *intent:* structure a large system as small, independently deployable functional units that may communicate sync or async. *When:* large systems needing independent deployment/scaling and team autonomy. *Trade-offs:* decoupling and independent lifecycles vs. distributed-system complexity (network, versioning, consistency).

**Model-View-Controller / Model-View-Presenter / Presentation-Abstraction-Control** - *intent:* separate UI presentation, control flow, and business model. *When:* interactive systems; MVP for testable rich clients with a passive view; PAC for complex hierarchically composed UIs. *Trade-offs:* swappable views and platform portability vs. added indirection.

**Dependency injection** - *intent:* an assembler resolves and injects concrete implementations of abstract dependencies at runtime. *When:* decoupling construction from use; enabling mock-based unit tests / TDD. *Trade-offs:* flexibility and testability vs. indirection and configuration.

**GoF design patterns (Adapter, Proxy, Facade, Observer, Strategy, Factory).** Implementation-level patterns supporting functionality; Adapter bridges incompatible interfaces, Facade simplifies a subsystem, Observer enables loose coupling between subject and observers. The boundary between architectural and design patterns is blurred.

## Principles & best practices

- **Reduce complexity, increase flexibility** - the two recurring goals behind every design principle.
- **Top-down and bottom-up** combine: decompose the problem while building up from proven components.
- **Divide and conquer / hierarchical decomposition** - break tasks until manageable; encapsulate complexity behind black-box boundaries.
- **As simple as possible, but no simpler** - prefer the less complex option when in doubt.
- **Separation of concerns**, especially separating functional from technical concerns, so each evolves independently.
- **Information hiding (Parnas)** - expose only what the task requires; access internals only via interfaces.
- **Low coupling, high cohesion** - minimize and weaken inter-block relationships; group classes by subsystem, not by type.
- **SOLID-style rules:** open/closed (open for extension, closed for modification, via abstraction/polymorphism); dependency inversion (depend on abstractions, not concretions - also enables mocking); interface segregation (split fat interfaces); Liskov substitution (subtypes must be substitutable). Resolve cyclic dependencies via abstraction + inheritance.
- **Regular refactoring and redesign** - software and its architecture degenerate without continuous tidying; budget time and resources for it.
- **Modularity** - self-contained modules with simple, stable relationships, achieved through separation of concerns + information hiding.
- **Architecture-centric development approaches** to be aware of: Domain-Driven Design (ubiquitous language, entities/value objects/services, aggregates/factories/repositories, core vs. generic vs. supporting subdomains, bounded contexts, anti-corruption layer); MDA/MDSD (generate code from models via PIM→PSM); aspect orientation for cross-cutting concerns; object orientation and procedural structuring.
- **Cross-cutting/technical concepts** to design deliberately: error handling (identify, classify, minimize, diagnose, prevent), security (authentication, authorization, integrity, confidentiality, non-repudiation, availability), persistence, logging, internationalization, deployment, communication. Address them early; reuse proven concepts across systems.
- **Documentation rules:** write from the reader's perspective; avoid unnecessary repetition; avoid ambiguity; explain non-standard notations; use as little formalism as necessary but as much as required; calibrate documentation depth to risk and complexity.
- **The architect's tasks (CPSA LG1-4):** decide system decomposition into building blocks, their dependencies and interfaces; decide cross-cutting technical concerns; communicate and document via views, patterns, and technical concepts; accompany realization and ensure code/architecture consistency; evaluate architecture for risk; identify and surface the consequences of decisions to stakeholders; recognize the need for iteration.

## Anti-patterns & pitfalls

- **Implicit / undesigned architecture** - every system has one; failing to design it deliberately makes future change unpredictable and expensive.
- **Degenerated design** - symptoms: fragility (a change breaks unrelated places), rigidity (simple changes ripple widely), low reusability (too many dependencies). Caused by changes that ignore the original structure.
- **Dereferencing chains** (`a.getB().getC().getD()`) create transitive dependencies across class networks; refactor them away.
- **Treating non-functional requirements as self-evident** - they are usually under-specified; the architect must surface and make them explicit (and traceable).
- **Mixing application (Blood Group A) and technical (Blood Group T) architecture layers.**
- **Over-formal documentation** - going off-schedule because every diagram detail must be perfect; resist dogmatism.
- **Neglecting external interfaces and the operations interface** - both are recurring real-world problem areas; invest early.
- **Square-as-subclass-of-rectangle** - a classic Liskov violation; inheritance misused for generalization/specialization.

## Decision guidance

- **Classify the system first.** Information vs. embedded vs. mobile immediately suggests default styles and the questions to ask (transactions and availability for information systems; timing guarantees and parallelism for embedded; UI vs. sensor balance for mobile).
- **Derive decision criteria from requirements before designing.** Analyze quality, flexibility (openness to change), and susceptibility to change of each requirement; use these as the criteria for decomposition and for choosing between equivalent alternatives. When two options score equally, go back up a level and look for missing requirements with the requirements engineer.
- **Choose rigor by risk.** Higher risk/complexity → more views, deeper building-block documentation, more evaluation. Lower risk → omit detail.
- **Pick views by stakeholder.** Use the four standard views as the baseline; add data, "big picture" (management), or mask views only when they help a specific stakeholder, accounting for the maintenance cost of each.
- **Achieve quality through tactics, not formulas.** For performance: load test, add hardware, reduce distribution/communication, reduce flexibility, add redundancy. For flexibility: information hiding, more abstraction layers, fewer dependencies, localized changes, decoupling via interfaces/adapters/facades/proxies. Every tactic has limits and trade-offs.
- **Evaluate qualitatively (ATAM) and quantitatively (metrics).** Use ATAM for scenario-based evaluation; use metrics (cyclomatic complexity, coupling, instability/abstractness, test coverage, change frequency, architecture-compliance checks) for trend detection and to locate critical elements. Quantitative metrics need functional/technical context to be meaningful.
- **Communicate decisions and their consequences.** It is the architect's responsibility to surface trade-offs and discuss them with stakeholders, adapting the level of detail to the audience (more for developers, less for customers/management).

### Evaluating architectures: methods

**ATAM (Architecture Tradeoff Analysis Method, SEI).** The leading scenario-based qualitative evaluation. Procedure (four phases): present the method; present business drivers; present the architecture (context, top-level blocks, key runtime scenarios); build and prioritize a **quality tree** whose leaves are concrete **scenarios**; analyze scenarios to classify decisions as **risks**, **sensitivity points** (small change → wide consequence), **trade-offs** (one decision affecting multiple qualities), and **non-risks**. Outputs: quality assessment against scenarios, risks for key scenarios, mitigation measures, risk-free scenarios.

**Quality scenarios.** Specify what an abstract quality actually means. Elements: trigger, source, environment, affected system artifact, response, response measure. Types: application/usage scenarios (runtime stimuli, performance), change scenarios (effort to add a feature or support), stress/limit scenarios (extreme situations, failures). Example: "Under normal load a price query must return in <5s; under heavy load up to 15s with a prior warning message."

**Prototypes and proof of concept.** Technical proof of concept verifies that components interact correctly (no real functionality). Prototypes (demonstration, real/analysis, laboratory sample, evolutionary pilot system) provide early feedback and reduce risk; beware throwaway prototypes that survive and prototypes substituting for documentation.

**Quantitative measures.** Metrics (LOC, cyclomatic/McCabe complexity = e − n + 2p, coupling, inheritance depth, instability, abstractness, distance, test coverage), architecture-compliance checking (do dependencies in code respect the layers?), repository analysis (duplicate code), and process analysis (change frequency).

## Source attribution

- **Core definitions, building blocks/interfaces, views, the architect's role, environment/influencing areas, the four design activities, Twin Peaks:** Gharbi, Koschel & Rausch (2019), chapters 2–4; reaffirmed in the 2024 2nd ed.
- **Design principles, heuristics, SOLID-style rules, DDD/MDA/aspect/OO approaches, architectural and design patterns:** Gharbi et al. (2019), chapter 3.
- **Views, templates, notations (UML), cross-cutting concepts, document types, documentation best-practice rules:** Gharbi et al. (2019), chapter 4.
- **Quality (ISO/IEC 25010), qualitative vs. quantitative evaluation, ATAM, quality scenarios and trees, prototypes, metrics:** Gharbi et al. (2019), chapter 5.
- **Tools awareness (requirements, modeling, generation, static/dynamic analysis, build, version, test, documentation tools):** Gharbi et al. (2019), chapter 6.
- **Evolutionary architecture, Conway's Law, and fitness functions** (additions in the updated CPSA-F curriculum): Gharbi, Koschel, Rausch & Tiemeyer (2024), section 3.5.

## Recent developments (2024–2026)

- **New CPSA-Foundation curriculum 2025.1 (effective April 1, 2025).** The iSAQB published version 2025.1 of the CPSA-F curriculum, resolving more than 250 issues versus 2023.1 and, most significantly, restructuring the curriculum around the four architectural tasks: clarifying requirements, designing the system, communicating, and evaluating/analyzing. This realignment makes the task-based framing in this file the explicit organizing spine of the certification. (iSAQB, "New CPSA-F curriculum version 2025.1 published," 2025, https://www.isaqb.org/news/new-cpsa-f-curriculum-version-2025-1-published/; iSAQB, "Insights into the Updated CPSA-Foundation Level Curriculum," https://www.isaqb.org/blog/insights-into-the-updated-cpsa-foundation-level-curriculum/)

- **Green software became a formal Advanced-Level module.** The iSAQB introduced an Advanced-Level GREEN module ("Green Software - Development of Resource-Efficient Applications"), covering Green Software Foundation patterns, the W3C Web Sustainability Guidelines, and energy-efficiency strategies (caching, CDN/edge). This institutionalizes sustainability/carbon as an architecture concern within the iSAQB body of knowledge, beyond the Foundation level. (iSAQB Advanced Level GREEN curriculum, https://public.isaqb.org/curriculum-green/curriculum-green-en.html)

- **Evolutionary architecture and fitness functions are now part of the canon.** The updated curriculum (reflected in the 2024 2nd-edition study guide, section 3.5) formally incorporates evolutionary architecture, Conway's Law, and fitness functions, aligning the iSAQB foundation with the Ford/Richards governance-as-code direction. (Gharbi, Koschel, Rausch & Tiemeyer, 2024, 2nd ed.)

- **AI in the architect's toolkit.** The broader 2025-2026 trend of architects validating AI-generated designs and governing AI agents (InfoQ trends report; "skills gap in the LLM era" research) is shaping how the durable iSAQB concepts (views, quality scenarios, ATAM, fitness functions) are applied, even though the certification keeps its technology-neutral stance. (InfoQ Software Architecture and Design Trends Report 2025, https://www.infoq.com/articles/architecture-trends-2025/)

- **Core body of knowledge stable.** Building blocks, interfaces, the four views, ISO/IEC 25010, ATAM, and quality scenarios are unchanged; the 2025.1 update is a restructuring around architectural tasks plus the formal addition of green software and evolutionary-architecture material.
