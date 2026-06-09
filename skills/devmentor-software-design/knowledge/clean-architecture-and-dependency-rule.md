# Clean Architecture and the Dependency Rule

**Domain:** 06 - Software Design Patterns and DDD
**Primary sources:** Clean Architecture: A Craftsman's Guide to Software Structure and Design (Martin, 2017); Mastering Clean Architecture (Fenwick, 2025); Clean Architecture: Comprehensive Beginners Guide (Wallace, 2020)

## Overview
Clean Architecture is a body of design principles and a concentric-layer architectural pattern, codified by Robert C. Martin, for keeping the business value of software soft (cheap to change) over its whole life cycle. Its central thesis is that a software system has two values: its behavior (what it does now) and its structure (how easily it can be changed). Structure is the greater value, because behavior that cannot be safely changed becomes a liability. The architecture protects structure by separating high-level policy (business rules) from low-level detail (frameworks, databases, UI, the web) and by controlling the direction of source-code dependencies so that details depend on policy and never the reverse.

This topic matters across the entire SDLC because architectural mistakes compound: they raise the cost of every future feature, defect fix, and deployment. The same ideas operate at three scales: SOLID principles arrange functions and classes (the bricks); component principles (cohesion REP/CCP/CRP and coupling ADP/SDP/SAP) arrange classes into deployable components (the rooms); and the Dependency Rule with its concentric layers arranges components into a whole system (the building). Fenwick (2025) restates these ideas with a practical, scalability-focused lens (dependency injection, DTOs, ports and adapters), while Wallace (2020) provides an accessible beginner restatement of the same Martin canon.

## Core concepts

**Behavior vs. structure (a tale of two values).** Behavior is the urgent, visible value; structure (the architecture) is the important but often non-urgent value. Eisenhower's matrix warns that the urgent crowds out the important. Architects must fight for structure, because a system that works today but cannot be changed will eventually cost more than it earns (Martin, 2017).

**Policy vs. detail.** Every system decomposes into policy (business rules and procedures, where the value lives) and details (IO devices, databases, web, frameworks, protocols) that merely let humans and machines communicate with the policy. The architect's job is to make the policy the most central, independent element and the details irrelevant to it, so decisions about details can be deferred. "A good architect maximizes the number of decisions not made" (Martin, 2017).

**Levels.** A component is higher level the farther it is from the inputs and outputs of the system. Entities are higher level than use cases; use cases are higher level than controllers and presenters. Higher-level policy must be protected from changes in lower-level detail.

## Frameworks & models

### SOLID principles (function/class level)

- **Single Responsibility Principle (SRP).** A module should have one, and only one, reason to change, meaning it should be responsible to one actor (a group of users or stakeholders). The classic violation is one class serving multiple actors (e.g., an `Employee` class with `calculatePay` for accounting, `reportHours` for HR, and `save` for DBAs); a change requested by one actor risks breaking another. Solution: separate code that serves different actors (e.g., split data from functions, optionally use a Facade). At the component level SRP becomes the Common Closure Principle; at the architecture level it drives the creation of boundaries along axes of change (Martin, 2017).

- **Open-Closed Principle (OCP).** Coined by Bertrand Meyer (1988): a software artifact should be open for extension but closed for modification. Extensions to requirements should require little or no change to existing code. Achieved by separating things that change for different reasons (SRP) and organizing dependencies (DIP) into a hierarchy where higher-level components are protected from changes in lower-level ones: "If component A should be protected from changes in component B, then B should depend on A." This is the fundamental architectural reason to study software design (Martin, 2017).

- **Liskov Substitution Principle (LSP).** Barbara Liskov (1988): subtypes must be substitutable for their base types without changing the correctness of programs that use them. The canonical violation is Square/Rectangle (a Square is not a substitutable Rectangle because width and height are not independently mutable). At architectural scale, LSP applies to any interface with multiple implementations (Java interfaces, REST contracts, duck-typed classes); a single substitutability violation (e.g., one taxi service abbreviating a field) pollutes the system with special-case mechanisms (Martin, 2017).

- **Interface Segregation Principle (ISP).** Do not force clients to depend on methods they do not use. Fat interfaces create accidental source-code dependencies that cause needless recompilation and redeployment in statically typed languages. The deeper, architecture-level lesson: depending on a module that carries baggage you do not need can cause troubles you did not expect (e.g., a framework F bound to a database D drags D's failures and changes into your system S) (Martin, 2017).

- **Dependency Inversion Principle (DIP).** The most flexible systems are those whose source-code dependencies point only at abstractions, not concretions. Concrete, volatile elements should be hidden behind stable abstract interfaces. Practical rules: do not refer to, derive from, or override volatile concrete classes; never mention the name of something concrete and volatile. Object creation is the hard case, handled with Abstract Factories so that source-code dependencies cross an architectural boundary toward the abstract side while the flow of control crosses the other way. DIP violations are gathered into a small `main`-like concrete component (Martin, 2017).

### Component cohesion principles (which classes belong in a component)

- **REP - Reuse/Release Equivalence Principle.** The granule of reuse is the granule of release. Classes grouped into a component must form a cohesive, releasable whole with a version number and release documentation, so consumers can decide when to adopt changes (Martin, 2017).
- **CCP - Common Closure Principle.** Gather into a component the classes that change for the same reasons and at the same times; separate those that change for different reasons. This is SRP for components: a component should have one reason to change. It minimizes the components touched by any given change (maintainability over reusability) (Martin, 2017).
- **CRP - Common Reuse Principle.** Do not force users of a component to depend on things they do not need. Classes that are reused together belong together; classes not tightly bound should not share a component. CRP is the component-level generalization of ISP (Martin, 2017).

These three pull against each other (the **cohesion tension diagram**): REP and CCP are inclusive (larger components); CRP is exclusive (smaller components). Early projects favor CCP (develop-ability); mature, widely reused projects shift toward REP/CRP (reusability). The right balance is dynamic and evolves over the project's life (Martin, 2017).

### Component coupling principles (relationships between components)

- **ADP - Acyclic Dependencies Principle.** Allow no cycles in the component dependency graph; keep it a directed acyclic graph (DAG). Cycles cause the "morning after syndrome," force components to be released and tested together, and remove any valid build order. Break cycles by applying DIP (invert one dependency) or by extracting a new shared component. The dependency structure is not designed top-down; it grows and "jitters" with the system and must be continuously monitored for cycles (Martin, 2017).
- **SDP - Stable Dependencies Principle.** Depend in the direction of stability. Stability is the effort needed to change a component; a component with many incoming dependencies (high fan-in) is stable. Instability `I = Fan-out / (Fan-in + Fan-out)` ranges 0 (maximally stable) to 1 (maximally unstable); the I metric should decrease in the direction of dependency. Components meant to be easy to change must not be depended on by hard-to-change components (Martin, 2017).
- **SAP - Stable Abstractions Principle.** A component should be as abstract as it is stable. Stable components should be abstract (interfaces/abstract classes) so they remain extensible (via OCP); unstable components should be concrete. Abstractness `A = Na / Nc` (abstract types over total types). SDP + SAP together are DIP for components. Plotting A against I gives the **Main Sequence** (the line from (1,0) to (0,1)); the **Zone of Pain** (stable + concrete, e.g., volatile DB schemas) and the **Zone of Uselessness** (abstract + no dependents) are to be avoided. Distance from the Main Sequence `D = |A + I - 1|` measures conformance and can be tracked over time (Martin, 2017).

### The Dependency Rule and the concentric layers

The overriding rule: **source-code dependencies must point only inward, toward higher-level policies.** Nothing in an inner circle may name anything in an outer circle (no functions, classes, variables, or data formats from the outside). The four canonical layers, innermost to outermost (Martin, 2017):

1. **Entities** - enterprise-wide Critical Business Rules and data; usable by many applications; least likely to change for external reasons.
2. **Use Cases** - application-specific business rules; orchestrate the "dance" of entities; isolated from UI/DB/framework concerns; use request/response models that carry no external dependencies.
3. **Interface Adapters** - controllers, presenters, gateways, ORMs/data mappers; convert data between the form convenient for use cases/entities and the form convenient for the DB, web, or external services. All SQL is confined here.
4. **Frameworks and Drivers** - the database, the web framework, the UI; the outermost "details," kept where they can do little harm. Mostly glue code.

There can be more than four circles; the rule is invariant. Boundaries are crossed using DIP: when an inner use case must call an outer presenter, the use case calls an inner **output port** interface that the presenter implements, so the source-code dependency still points inward while control flows outward. **Data crossing a boundary** is always a simple, isolated structure (DTO/struct) shaped for the inner circle; never pass Entity objects or database rows across.

## Patterns

(Also added to PATTERN-CATALOG.md.)

- **Clean Architecture (concentric layers + Dependency Rule)** - Intent: isolate business rules from frameworks, UI, and databases so details are pluggable and policy is testable in isolation. When to use: systems expected to outlive their frameworks/data stores, or needing high testability. Trade-offs: more interfaces, indirection, and DTO mapping in exchange for independence, testability, and deferred decisions. *(Martin, 2017; Fenwick, 2025; Wallace, 2020)*

- **Ports and Adapters / Hexagonal Architecture (Cockburn)** - Intent: surround the application core with symmetric ports (interfaces) implemented by driving and driven adapters, so the core depends on nothing external. When to use: any system wanting to swap UIs, data stores, or external services and to test the core headlessly. Trade-offs: more wiring; a clearer formalization of boundary contracts. Clean Architecture, Hexagonal, Onion, DCI, and BCE all share the same goal (separation of concerns by layering with inward dependencies). *(Martin, 2017; Fenwick, 2025)*

- **Onion Architecture** - Intent: concentric rings with the domain model at the center and infrastructure at the edge, dependencies pointing inward. When to use: domain-centric applications. Trade-offs: equivalent to Clean/Hexagonal; chiefly a vocabulary difference. *(Martin, 2017; Fenwick, 2025)*

- **Screaming Architecture** - Intent: structure top-level packages and directories around the use cases and business domain (e.g., "Inventory Management") rather than the framework (e.g., "Rails"/"Spring"), so the architecture announces what the system does. When to use: every application; especially valuable for onboarding and longevity. Trade-offs: requires resisting framework-first scaffolding; pays off in clarity and deferrable framework decisions. *(Martin, 2017; Fenwick, 2025)*

- **Humble Object** - Intent: split a unit into a hard-to-test "humble" part (stripped to bare essence, e.g., the View) and an easy-to-test part (e.g., the Presenter) so that an architectural boundary coincides with a testability boundary. When to use: GUIs, database gateways, ORMs/data mappers, service listeners. Trade-offs: extra class/indirection for a large gain in testability. *(Martin, 2017)*

- **Partial boundary** - Intent: do the full design work for a boundary (reciprocal interfaces, input/output structures) but keep both sides in one component, or use a one-dimensional/facade boundary, deferring full separation. When to use: a boundary may be needed later but full separation is not yet worth its cost. Trade-offs: holds the option open at lower ongoing cost; risk that the latent boundary erodes if it never materializes. *(Martin, 2017)*

- **Abstract Factory for dependency creation** - Intent: let high-level policy create instances of volatile concrete types without a source-code dependency on them. When to use: enforcing DIP across a boundary. Trade-offs: indirection; concentrates the unavoidable concrete dependency in a single place. *(Martin, 2017)*

## Principles & best practices
- Keep the business rules pristine; let UI, DB, and frameworks plug in to them, not the other way around.
- Defer detail decisions (database, web server, framework, DI container) as long as possible; this leaves room for experiments and better-informed choices.
- Treat frameworks with a jaded eye: they are tools to be used, not architectures to conform to. Keep them at arm's length so use cases are unit-testable without them.
- Express boundaries with DIP: inner interfaces (ports) implemented by outer classes (adapters); control flows out, dependencies point in.
- Pass simple data structures across boundaries; never leak Entity objects or DB rows inward.
- Use dependency injection (constructor injection preferred) to satisfy dependencies without violating the Dependency Rule (Fenwick, 2025). Prefer DI over service locators, which hide dependencies.
- Use DTOs and adapters to prevent persistence/entity leakage into business logic (Fenwick, 2025).
- Pursue high cohesion and low coupling; design for change by isolating volatility (Fenwick, 2025).
- Let the architecture grow: the component dependency structure is discovered and refined, not designed top-down.

## Anti-patterns & pitfalls
- **Framework-driven architecture.** Letting Rails/Spring/ASP dictate structure; the architecture screams the framework, not the domain, and use cases cannot be tested without it.
- **Dependency cycles.** Violating ADP produces the morning-after syndrome, forced co-release, and no valid build order.
- **SDP/SAP violations.** A stable component depending on a volatile one; or a concrete, stable, heavily depended-on component (Zone of Pain, e.g., a volatile shared DB schema); or abstract components nobody uses (Zone of Uselessness).
- **Leaking details inward.** Passing database rows, ORM entities, or `HttpRequest`/`HttpResponse` types across boundaries; allowing SQL outside the interface-adapters layer.
- **One class serving many actors (SRP violation).** Accidental coupling where a change for one stakeholder breaks another.
- **Fat interfaces / fat dependencies (ISP violation).** Depending on modules carrying baggage you do not use.
- **Anticipatory full boundaries everywhere.** Over-applying boundaries violates YAGNI; partial boundaries are the measured compromise.
- **"ORM" misconception.** Objects are not data structures; ORMs are really data mappers and belong in the database layer as Humble Objects.

## Decision guidance
- **Where to put a piece of logic?** Ask who it serves (which actor) and how/when it changes. Enterprise-wide rules independent of any application -> Entities. Application-specific orchestration -> Use Cases. Format conversion for an external agency -> Interface Adapters. A framework/DB/web concern -> Frameworks and Drivers.
- **Which classes share a component?** Apply CCP (change together) first for develop-ability; shift toward CRP/REP as reuse grows. Resolve the tension consciously and expect it to move over time.
- **Which way should a dependency point?** Toward stability and abstraction (SDP + SAP). If component A must be protected from B, make B depend on A. Invert with DIP wherever control must flow against the desired dependency.
- **Full or partial boundary?** Build a full boundary only where independent deployability/replaceability has clear present value; otherwise hold the place with a partial boundary to honor YAGNI while keeping the option.
- **Is this a detail?** The web, the database, the UI, the protocol are details. If a decision can be deferred without affecting business policy, defer it.
- **Microservices vs. boundaries.** Services that merely separate behaviors are not architecture; architecture is defined by boundaries that separate policy from detail and follow the Dependency Rule. Design service internals with the same rule (Martin, 2017).

## Source attribution
- SOLID principles, component cohesion (REP/CCP/CRP) and coupling (ADP/SDP/SAP), stability/abstractness metrics and the Main Sequence, the Dependency Rule and the four layers, boundaries, Humble Object, Screaming Architecture, partial boundaries, behavior-vs-structure, policy-vs-detail, and the framing of Hexagonal/Onion/DCI/BCE as kin: Martin (2017), Clean Architecture.
- Practical application: dependency injection vs. service locators, DTOs and adapters as boundary tools, ports-and-adapters as boundary formalization, high cohesion / low coupling, design-for-change, and the scalability-oriented restatement of the layers and Screaming Architecture: Fenwick (2025), Mastering Clean Architecture.
- Accessible beginner-level restatement of SOLID, component principles, the Dependency Rule, entities/use cases, and boundaries: Wallace (2020), Clean Architecture: Comprehensive Beginners Guide.

## Recent developments (2024–2026)
- **"Code for the AI reader" challenges layer indirection.** A 2026 argument holds that classic Clean Architecture indirection (deep abstraction, interface-implementation separation, polymorphism) helps human cognition but hurts LLM agents, which read code linearly and must traverse layers to resolve a concrete call. The advice is to favor explicit, locally readable code over implicit architectural convention when optimizing for AI-assisted maintenance. (dasroot.net, "Code for the AI Reader: Redesigning Architecture for the LLM Era", 2026, https://dasroot.net/posts/2026/05/code-for-ai-reader-redesigning-architecture-llm-era/)
- **Vertical Slice Architecture gains ground as a stated alternative.** Practitioners increasingly position feature-first "vertical slices" (each use case bundling its command, query, domain logic, persistence, and validation) against horizontal Clean Architecture layering, arguing slices shrink the change blast radius and reduce ceremony for typical apps. (Medium, "Vertical Slice Aggregates: A Real Alternative to Clean Architecture in .NET", 2025, https://medium.com/@vahidbakhtiaryinfo/vertical-slice-aggregates-a-real-alternative-to-clean-architecture-in-net-03dd9e03d68d)
- **Clean vs. Hexagonal debate stays active.** 2025 deep-dives continue to treat Clean Architecture as a unification of Hexagonal (ports/adapters) and Onion, with some engineers preferring Hexagonal as leaner; the recurring critique is over-boilerplate and over-abstraction for simpler systems. (vinaypal.com, "Clean Architecture vs Hexagonal Architecture", 2025, https://www.vinaypal.com/2025/04/clean-architecture-vs-hexagonal.html)
- **SOLID and the Dependency Rule remain canonical but selectively applied.** The principles themselves are uncontested; the 2024-2026 shift is pragmatic, applying full boundaries only where independent deployability/testability clearly pays, consistent with the file's partial-boundary and YAGNI guidance.
- **AI codegen reintroduces design-quality concerns.** Studies of LLM refactoring find generated code often violates required project patterns and SOLID, reinforcing that human architectural judgment (where Clean Architecture's separation of policy from detail still applies) outperforms naive prompting. (ScienceDirect, "Comparative analysis of design pattern implementation validity in LLM-based code refactoring", 2025, https://www.sciencedirect.com/science/article/abs/pii/S0164121225001876)
