# Components, Modularity, Coupling, and Cohesion

**Domain:** 04 - Software Architecture Fundamentals
**Primary sources:** Fundamentals of Software Architecture (Richards & Ford, 2020); Software Architecture: The Hard Parts (Ford, Richards, Sadalage & Dehghani, 2021); Head First Software Architecture (Gandhi, Richards & Ford, 2023); Software Architecture for Developers (Abrams, 2024)

## Overview
Modularity is the organizing principle of software architecture: how related code is grouped and how the groups depend on one another. Architects rarely see a requirement that says "ensure good modularity," yet sustainable systems require it, which makes modularity a textbook implicit characteristic. The tools architects use to reason about structure (metrics, fitness functions, visualizations) all rest on the vocabulary of modules, components, cohesion, coupling, and connascence. Software systems, like physical systems, tend toward entropy; architects must constantly spend energy to preserve structural order.

This note covers the building blocks (modules vs components), how to partition them (technical vs domain), how to measure modularity (cohesion, coupling, connascence, and the derived metrics), and how to find the right component granularity. These ideas are foundational because they determine how easily a system can change, be tested, be deployed, and eventually be decomposed into a distributed architecture.

## Core concepts

**Module vs component.** A *module* is a logical grouping of related code (a Java package, a .NET namespace, a group of functions). A *component* is the *physical* packaging of modules: a jar, dll, gem, a library, a layer/subsystem, or a service. Components are the lowest level of the system an architect usually interacts with directly; class and function design below that belongs mostly to developers and tech leads. A component should always have a well-defined role, responsibility, and set of operations, and is typically manifested as a directory/namespace (for example, `app/order/payment`).

**Cohesion.** The degree to which the parts of a module belong together. A cohesive module is one where splitting it would force the pieces to call each other across module boundaries. Constantine: "Attempting to divide a cohesive module would only result in increased coupling and decreased readability." Cohesion measures, best to worst: functional, sequential, communicational, procedural, temporal, logical, coincidental.

**Coupling.** The degree to which code artifacts depend on one another. *Afferent* coupling counts incoming connections to an artifact; *efferent* coupling counts outgoing connections (Yourdon & Constantine, 1979). The Hard Parts uses the simplest working definition of coupling overall: two parts are coupled if a change in one might force a change in the other.

**Connascence (Page-Jones, 1996).** Two components are connascent if a change in one would require the other to be modified to keep the system correct. Connascence refines afferent/efferent coupling into *how* things are coupled, not just *whether*. It comes in static and dynamic forms.

**Architecture quantum.** An independently deployable artifact with high functional cohesion and synchronous connascence. The quantum is the unit at which architecture characteristics should be scoped (see the characteristics note). It is the bridge between low-level coupling/cohesion concepts and high-level decisions about monolith vs distributed.

## Frameworks & models

**Cohesion measures (Richards & Ford, best to worst).**

- *Functional* - every part is essential and related; the module contains everything it needs to function.
- *Sequential* - one module's output is another's input.
- *Communicational* - modules form a chain operating on shared information (add a record, then email about it).
- *Procedural* - modules must execute in a specific order.
- *Temporal* - modules are related only by timing (startup initialization tasks).
- *Logical* - operations are logically grouped but functionally different (a `StringUtils` of unrelated static methods).
- *Coincidental* - elements share a file but nothing else; the worst form.

**LCOM (Lack of Cohesion in Methods).** A structural metric (Chidamber & Kemerer suite) that exposes incidental coupling within a class: the sum of sets of methods not shared via shared fields. A high LCOM score means a class is doing unrelated things and could be split. Useful when migrating styles to find utility classes that were never cohesive. Limitation: it finds only *structural* lack of cohesion, not logical fit (Second Law: prefer *why* over *how*).

**Derived coupling metrics (Robert Martin).**

- *Abstractness* (A) = abstract artifacts / (abstract + concrete artifacts). A `main()` with 5,000 lines scores near 0.
- *Instability* (I) = efferent / (efferent + afferent coupling). High instability means a component breaks easily when its many dependencies change. A value near 0 may be stable (mostly abstract) or rigid (mostly concrete); A and I must be read together.
- *Distance from the main sequence* (D) = |A + I − 1|. The main sequence is the ideal balance line. Components far in the upper-right fall into the **zone of uselessness** (too abstract to use); far in the lower-left fall into the **zone of pain** (too concrete, brittle, hard to maintain). The Hard Parts uses this metric to judge whether a codebase is even worth restructuring.

**Static connascence (source-level coupling, weakest to strongest).**

- *Connascence of Name (CoN)* - components must agree on a name. Most desirable; trivial to refactor.
- *Connascence of Type (CoT)* - agree on a type.
- *Connascence of Meaning/Convention (CoM/CoC)* - agree on the meaning of values (magic numbers vs named constants).
- *Connascence of Position (CoP)* - agree on the order of values (positional parameters).
- *Connascence of Algorithm (CoA)* - agree on an algorithm (a hash that must match on client and server).

**Dynamic connascence (runtime coupling, harder to detect).**

- *Connascence of Execution (CoE)* - order of execution matters.
- *Connascence of Timing (CoT)* - timing matters (race conditions).
- *Connascence of Values (CoV)* - related values must change together (distributed transactions).
- *Connascence of Identity (CoI)* - components must reference the same entity (a shared distributed queue).

**Connascence properties and the three rules.** Connascence is assessed by *strength* (how easily it can be refactored; static beats dynamic), *locality* (how close the modules are; the same strong connascence is worse across modules than within one), and *degree* (how many artifacts are affected). Page-Jones's guidelines: (1) minimize overall connascence by encapsulating; (2) minimize connascence that crosses encapsulation boundaries; (3) maximize connascence within boundaries. Jim Weirich's two rules: *Rule of Degree* - convert strong forms to weaker forms; *Rule of Locality* - as elements get farther apart, use weaker connascence.

**Unifying coupling and connascence.** Static connascence is essentially a refinement of afferent/efferent coupling describing *how* the coupling manifests. Communication connascence (synchronous vs asynchronous, added in the quantum discussion) extends this into distributed architectures, where architects care more about communication style than about low-level degree.

**Top-level partitioning: technical vs domain (Richards & Ford).** The most consequential early decision is how to partition components at the top level.

- *Technical partitioning* - organize by technical capability (presentation, business, persistence). This is the layered monolith. Strength: clear separation of technical concerns and decoupling between layers. Weakness: any single domain (for example, CatalogCheckout) is smeared across all layers, making change harder; tends toward higher global coupling and a single shared database.
- *Domain partitioning* - organize by workflow/domain (inspired by DDD). This is the modular monolith and microservices. Strengths: models the business, supports the Inverse Conway Maneuver and cross-functional teams, message flow matches the domain, and it migrates cleanly to distributed architectures. Weakness: customization/common code can be duplicated across domains.

**Conway's Law and the Inverse Conway Maneuver.** Organizations produce designs that mirror their communication structures. The Inverse Conway Maneuver (Jonny Leroy) deliberately reshapes teams to promote the desired architecture.

## Patterns
*(See PATTERN-CATALOG.md for cross-domain pattern entries.)*

**Modular monolith** - Intent: a single deployable unit partitioned around domains (not technical layers) with a single database. When to use: as a strong default for new systems, and as a stepping-stone toward microservices. Trade-offs: keeps deployment simple while still aligning code with the business; can develop hidden coupling if module boundaries are not governed.

**Component identification (iterative) (Richards & Ford)** - Intent: discover the right coarse-grained building blocks. Cycle: identify initial components → assign requirements/stories to them → analyze roles and responsibilities → analyze architecture characteristics → restructure → repeat. When to use: at the start of every new system. Trade-offs: a first draft is never right; the value is in iterating with developers.

**Component discovery techniques** - *Actor/Actions* (map roles and their actions to components; good general-purpose default, works for monolith or distributed); *Event storming* (DDD/microservices, builds components around events/messages); *Workflow approach* (like event storming but without requiring messaging). None is superior; each fits a different process.

## Principles & best practices

- **Build services from components, not classes (Hard Parts).** When decomposing a monolith, the unit of extraction should be a well-defined component (namespace/directory), never an individual class.
- **Prefer static connascence to dynamic, and weaker forms to stronger.** Refactor connascence of meaning into connascence of name (named constants), positional parameters into named ones, and so on.
- **Maximize connascence inside a module, minimize it across boundaries.** Encapsulation is the primary tool for controlling overall connascence.
- **Use coupling metrics to judge restructurability.** Before migrating a monolith, check distance from the main sequence; a codebase mostly in the zones of pain/uselessness may not be worth shoring up.
- **Get granularity deliberately.** Too fine-grained means excessive inter-component communication; too coarse-grained means high internal coupling and poor deployability/testability.
- **Default to domain partitioning** for systems likely to grow or distribute, while recognizing the First Law trade-offs.
- **Keep reuse low and inheritance shallow in a starter layered monolith** so a later move to another style stays feasible.

## Anti-patterns & pitfalls

- **Big Ball of Mud (Foote & Yoder, 1997).** No discernible structure; information shared promiscuously and globally. Hard to change, test, scale, or deploy. For these, tactical forking (see the Hard Parts note) is often the only practical restructuring path.
- **Entity trap.** Building a `Manager` component per database entity. This is an object-relational mapping, not an architecture; components become too coarse-grained and offer no real workflow structure.
- **Cyclic dependencies between components.** A network of mutually referencing components destroys reuse and trends toward a Big Ball of Mud. Govern with a cycle-detection fitness function.
- **Incidental (coincidental) cohesion.** Utility classes grouping unrelated methods; flagged by a high LCOM score.
- **Auto-import sprawl.** Developers reflexively importing across the codebase erode modularity faster than code reviews can catch.
- **Architecture sinkhole** (in technical partitioning) - requests passing through layers with no logic added; acceptable for ~20% of requests, a smell at 80%.

## Decision guidance

1. **Choose the top-level partitioning first** - technical (layered) or domain (modular monolith / microservices). This sets the style and how components will be identified.
2. **Identify components iteratively** using actor/actions, event storming, or the workflow approach, then assign requirements and refine.
3. **Analyze characteristics per component**, not just function. Differing characteristics (one part needs high scale, another does not) justify splitting components and may force a distributed architecture (count the quanta).
4. **Tune granularity** by watching inter-component chatter (too fine) against internal coupling, deployability, and testability (too coarse).
5. **Measure structure** with cohesion (LCOM), coupling (afferent/efferent, instability), and distance from the main sequence; use connascence strength/locality/degree to decide what to refactor.
6. **Decide monolith vs distributed** by counting architecture quanta: one set of characteristics favors a monolith; multiple sets favor distribution.

## Source attribution

- Module vs component, cohesion measures, LCOM, afferent/efferent coupling, abstractness/instability/distance from the main sequence (zones of pain and uselessness), static and dynamic connascence, connascence properties and rules, the unifying coupling/connascence view, technical vs domain partitioning, Conway's Law and the Inverse Conway Maneuver, the component identification cycle, discovery techniques, the entity trap, and the architecture quantum all come from **Fundamentals of Software Architecture, Chapters 3, 7, 8 (Richards & Ford, 2020).**
- "Build services from components not classes," the abstractness/instability/distance metrics used to judge decomposability, and granularity disintegrators/integrators come from **Software Architecture: The Hard Parts, Chapters 2, 4, 5, 7 (Ford et al., 2021).**
- Logical components as a dimension of architecture and the namespace/directory mapping come from **Head First Software Architecture, Chapter 1 (Gandhi, Richards & Ford, 2023).**
- Modularity, loose coupling, high cohesion, encapsulation, and SRP as guiding principles are corroborated by **Software Architecture for Developers (Abrams, 2024).**

## Recent developments (2024–2026)

- **Modularity is having a moment via the modular monolith.** The 2024-2026 swing back toward modular monoliths put cohesion, coupling, and module-boundary governance back at the center of mainstream practice: well-defined modules communicating through explicit interfaces, with separate schemas inside one database, are now the recommended default for most new systems. This is exactly the domain-partitioned modular monolith this file describes, now treated as a destination rather than only a stepping-stone. (Java Code Geeks, "Microservices vs. Modular Monoliths in 2025," https://www.javacodegeeks.com/2025/12/microservices-vs-modular-monoliths-in-2025-when-each-approach-wins.html)

- **AI applied to coupling analysis and boundary detection.** LLMs and graph-based machine learning are increasingly used to compute and visualize component dependencies and to propose where to split or merge components, automating parts of the iterative component-identification cycle. Graph representations of the codebase (the same afferent/efferent dependency structure this file relies on) dominate the automated approaches. (Springer, "A Pattern-Driven and LLM-Assisted Approach for Decomposing Monolithic Systems into Microservices," 2025, https://link.springer.com/chapter/10.1007/978-981-95-5012-8_16; ResearchGate, "MonoEmbed," 2025, https://www.researchgate.net/publication/397384208_MonoEmbed)

- **Fitness-function-style cycle and coupling checks are now AI-generable.** With Architecture as Code (Ford & Richards, 2025), the cycle-detection, layering, and coupling-threshold checks this file recommends governing can be generated from a platform-neutral definition language via LLMs, lowering the cost of enforcing connascence/coupling rules continuously. (O'Reilly, *Architecture as Code*, 2025, https://www.oreilly.com/library/view/architecture-as-code/9798341640368/)

- **Concepts unchanged.** Cohesion measures, connascence (static/dynamic, strength/locality/degree), the derived Martin metrics, technical-vs-domain partitioning, and the architecture quantum are all as Richards & Ford defined them; the 2024-2026 development is renewed industry emphasis on modular boundaries plus AI tooling that helps measure and act on coupling.
