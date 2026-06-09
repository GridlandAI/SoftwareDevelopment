# Domain-Driven Design: Tactical Design

**Domain:** 06 - Software Design Patterns and DDD
**Primary sources:** Implementing Domain-Driven Design (Vaughn Vernon, 2013); Learning Domain-Driven Design (Vladik Khononov, 2021)

## Overview
Tactical DDD is the set of building blocks and architectural choices used to implement a model *inside* a single bounded context. Where strategic design decides what the contexts are and how they relate, tactical design decides how to express the model in code so that the code speaks the ubiquitous language, protects business invariants, and remains maintainable as the domain evolves. The canonical tactical patterns (entities, value objects, aggregates, domain events, repositories, factories, domain services, application services) originate in Eric Evans's work and are codified for implementation by Vernon and Khononov.

A crucial framing from Khononov: these tactical patterns are the building blocks of one specific business-logic pattern, the **domain model**, which is appropriate for complex logic (typically core subdomains). They are not mandatory for all DDD. Simpler subdomains are better served by transaction script or active record. Tactical design therefore includes choosing the right business-logic pattern, the right architecture (layered, ports and adapters, CQRS), and the right testing strategy for the subdomain at hand. This matters throughout the SDLC: it governs how cleanly business rules are encoded, how the system scales, how it is tested, and how integration and consistency are handled.

## Core concepts

### Business-logic implementation patterns
Four ways to implement business logic, from simplest to most powerful (Khononov 2021, ch. 5–7; Vernon draws on the same Fowler lineage):

- **Transaction script** - organize logic as procedures, one per system operation, each wrapped in a transaction. For simple logic and simple data structures.
- **Active record** - data structures that wrap database access and carry simple CRUD logic. For simple logic with more complex data structures; orchestrated by a service layer.
- **Domain model** - an object model combining behavior and data, built from value objects, entities, aggregates, domain events, and domain services, free of infrastructural concerns ("plain old objects"). For complex business logic (rules, invariants, state machines), that is, core subdomains.
- **Event-sourced domain model** - a domain model whose aggregate state is persisted as a sequence of domain events rather than as current state. For complex core logic that needs an audit log, deep behavioral analysis, or models money/transactions.

### The domain-model building blocks

**Value object** - an object identified entirely by the composition of its values; no identity field. Immutable (a change conceptually yields a new value), so it is side-effect-free and thread-safe; equality is by value, so equality and hash must be implemented explicitly. Value objects model not just data but the behavior that manipulates it (parsing, validation, conversion, comparison), centralizing logic that would otherwise scatter. Use them wherever possible, especially for properties of entities, statuses, and money. They cure the "primitive obsession" smell. (Khononov 2021, ch. 6; Vernon 2013, ch. 6)

**Entity** - an object that needs an explicit identity field to distinguish instances even when all other fields match (two people with the same name are different people). The identity is (almost always) immutable for the entity's lifetime; the rest of the state is mutable. Entities are not used independently in DDD; they live inside an aggregate. (Khononov 2021, ch. 6; Vernon 2013, ch. 5)

**Aggregate** - a cluster of entities and value objects that share a transactional consistency boundary and are treated as one unit. The aggregate is the central tactical concept. Its responsibilities:
- *Consistency enforcement boundary.* Only the aggregate's own business logic may mutate its state; outside code may only read. State changes go through the aggregate's public methods (commands), which validate input and enforce every invariant in one place.
- *Transactional boundary.* All changes to one aggregate commit atomically; one aggregate instance per database transaction. Needing to modify several aggregates in one transaction signals wrong boundaries.
- *Aggregate root.* One designated entity is the only externally addressable interface; internal entities (for example messages inside a ticket) are reached only through the root.
- *Reference other aggregates by identity*, not by object reference, to keep boundaries crisp and transactions per-aggregate. Information that may be eventually consistent belongs outside the aggregate.
- *Concurrency control.* Use an aggregate version field and optimistic concurrency so a later transaction cannot blindly overwrite an earlier one.
(Khononov 2021, ch. 6; Vernon 2013, ch. 10)

**Domain event** - an immutable message describing something significant that has already happened in the domain, named in the past tense (TicketEscalated, BacklogItemCommitted), carrying the relevant data. Aggregates publish domain events; other aggregates, processes, or external systems subscribe. Domain events are part of the aggregate's public interface and the mechanism for eventual consistency across aggregates and contexts. (Khononov 2021, ch. 6; Vernon 2013, ch. 8)

**Domain service** - a stateless object hosting business logic that does not naturally belong to a single aggregate or value object, or that needs to read data from several aggregates to compute a result (for example calculating an SLA deadline from a ticket, a department policy, and a work schedule). A domain service is not a loophole around the one-aggregate-per-transaction rule; it coordinates *reads*. It has nothing to do with microservices or SOA. (Khononov 2021, ch. 6; Vernon 2013, ch. 7)

**Factory** - encapsulates the creation of complex aggregates or value objects so that a fully valid instance is produced atomically, keeping construction logic out of the entities themselves. (Vernon 2013, ch. 11)

**Repository** - provides collection-like access to aggregates, hiding persistence behind an interface owned by the domain. One repository per aggregate type; it loads and saves whole aggregates and is the natural place for the optimistic-concurrency check. (Vernon 2013, ch. 12)

**Application service (service layer)** - thin orchestration at the system boundary. It loads the relevant aggregate, executes one command on it, persists it, dispatches resulting domain events, and returns a result. It contains no business logic itself; it coordinates a transaction and translates between the outside world and the domain. (Khononov 2021, ch. 6 and ch. 8; Vernon 2013, ch. 14)

### Modules
Within a bounded context, group related domain objects into modules (namespaces/packages) named in the ubiquitous language. Modules provide vertical partitioning that, together with horizontal layers, keeps a monolithic context modular and prevents a big ball of mud; these logical boundaries can later be promoted to physical bounded contexts. (Vernon 2013, ch. 9; Khononov 2021, ch. 8)

## Frameworks & models

### Architectural patterns within a context (Khononov 2021, ch. 8; Vernon 2013, ch. 4)
- **Layered architecture** - horizontal layers (presentation/user-interface, business logic/domain, data access/infrastructure), with each layer depending only on the one beneath. An optional **service/application layer** sits between presentation and business logic to host orchestration. Good fit for transaction script and active record; awkward for a domain model because the top-down dependency forces business objects to know about infrastructure. Note: layers are logical boundaries; tiers are physical.
- **Ports and adapters (hexagonal / onion / clean)** - apply the dependency inversion principle so the business logic sits at the center and depends on nothing. Infrastructure concerns are unified into one layer; the business logic defines abstract *ports* (interfaces) that the infrastructure implements as *adapters*, wired by dependency injection. An application layer fronts the system as its public interface. This is the natural home for the domain model and event-sourced domain model because it keeps aggregates and value objects persistence-ignorant. The named variants are conceptually identical.
- **CQRS (command-query responsibility segregation)** - segregate the write side (a single strongly consistent *command execution model*, the source of truth, with optimistic concurrency) from one or more read-only *projections* (read models). Projections can live in different databases (polyglot persistence) and be rebuilt from scratch. Projections are generated synchronously (catch-up subscription with a checkpoint, easy to add/rebuild) or asynchronously (publish committed changes to a bus, more scalable but exposed to out-of-order/duplicate delivery). A command may and often should return data from the strongly consistent model. CQRS is required for the event-sourced domain model (events alone cannot be queried by state) and useful whenever the same data needs multiple persistent representations.

Scope matters: layered, ports-and-adapters, and CQRS are not system-wide mandates. A single bounded context may contain subdomains of different types, each warranting a different business-logic and architectural pattern. Use vertical module boundaries so each slice can use the appropriate tools (architectural slices).

### Event sourcing (Khononov 2021, ch. 7)
Instead of storing current state, persist the full sequence of domain events for each aggregate; current state is a left-fold (projection) over the events. This adds the dimension of time: you can answer questions the state-based model loses (how many calls before a lead converted, the full lifecycle history). Benefits include a complete audit log and the ability to derive new projections retroactively. Costs include the need for CQRS to query, schema/versioning of events, and the discipline of modeling change as events.

### Eventual consistency and the "ask whose job it is" heuristic (Vernon 2013, ch. 10)
Because only one aggregate is modified per transaction, changes that span aggregates use eventual consistency: an aggregate command publishes a domain event; an asynchronous subscriber loads a different aggregate and acts on it in its own transaction, with retries (capped exponential back-off) and compensation on failure. To decide whether a rule needs transactional or eventual consistency, ask whether it is the *job of the user executing this use case* to keep the data consistent. If yes, make it transactionally consistent within the aggregate; if it is another user's or the system's job, allow eventual consistency. Domain experts often tolerate delays developers assume are unacceptable.

## Patterns
For each: name, intent, when to use, trade-offs. (Also appended to PATTERN-CATALOG.md.)

- **Value object** - Intent: model a domain concept by its values, immutably, with behavior. When: properties of entities, statuses, money, anything identifiable by value. Trade-offs: extra small types and equality code, in exchange for expressiveness, safety, and centralized logic; eliminates primitive obsession.
- **Entity** - Intent: model a domain concept with a distinct, stable identity and mutable state. When: instances must be distinguished beyond their attribute values. Trade-offs: requires identity management; in DDD always used within an aggregate.
- **Aggregate (+ aggregate root)** - Intent: a transactional consistency boundary clustering entities/value objects, mutated only through its root. When: complex invariants must always hold across a small cluster of objects. Trade-offs: forces one-instance-per-transaction discipline and cross-aggregate eventual consistency; design small to protect performance and scalability.
- **Domain event** - Intent: an immutable past-tense record of something significant, published for subscribers. When: integrating aggregates, driving eventual consistency, communicating across contexts, event sourcing. Trade-offs: requires reliable publishing (see Outbox) and out-of-order/duplicate handling.
- **Domain service** - Intent: stateless host for logic spanning multiple aggregates or belonging to none. When: a calculation needs data from several aggregates or has no natural object home. Trade-offs: easy to overuse as a dumping ground; must not violate one-aggregate-per-transaction; coordinates reads, not multi-aggregate writes.
- **Factory** - Intent: encapsulate complex, invariant-respecting construction. When: building an aggregate/value object requires non-trivial assembly. Trade-offs: extra indirection; keeps entities free of construction concerns.
- **Repository** - Intent: collection-like, persistence-ignorant access to whole aggregates. When: any aggregate that must be stored and retrieved. Trade-offs: one per aggregate type; the boundary where concurrency control lives.
- **Application service** - Intent: thin transactional orchestration at the system boundary, no business logic. When: every entry point into a domain-model context. Trade-offs: must stay thin; business logic leaking here is a smell.
- **Domain model (business-logic pattern)** - Intent: encode complex logic as behavior-rich objects that speak the ubiquitous language. When: core subdomains with complex rules/invariants. Trade-offs: more design effort; overkill for CRUD/supporting subdomains.
- **Event-sourced domain model** - Intent: a domain model persisted as a stream of domain events. When: complex core logic needing audit, time-travel, or deep analytics; money. Trade-offs: requires CQRS for querying, event versioning, and a consistency mindset.
- **Layered architecture** - Intent: separate presentation, business logic, and data access by top-down dependency. When: transaction-script/active-record contexts. Trade-offs: simple; ill-suited to a persistence-ignorant domain model.
- **Ports and adapters (hexagonal/onion/clean)** - Intent: invert dependencies so business logic depends on nothing; infrastructure implements ports. When: domain-model and event-sourced contexts. Trade-offs: more interfaces and wiring, in exchange for testability and persistence ignorance.
- **CQRS** - Intent: segregate the strongly consistent write model from rebuildable read projections. When: multiple persistent representations of data, polyglot persistence, or event sourcing. Trade-offs: projection lag/eventual consistency and projection maintenance; mandatory with event sourcing.
- **Outbox** - Intent: reliably publish domain events by committing them in the same transaction as the aggregate, then relaying them to the bus (polling or transaction-log tailing) and marking them published. When: any time aggregate changes must be reliably reflected in published events. Trade-offs: at-least-once delivery (subscribers must be idempotent); needs a relay process. (Khononov 2021, ch. 9)
- **Saga** - Intent: coordinate a business process spanning multiple aggregates/contexts by listening to events and issuing commands, with compensating actions on failure. When: a long-running process (in transactions, not necessarily time) crosses aggregate boundaries. Trade-offs: distributed-systems complexity; stateful sagas can be implemented as event-sourced aggregates. (Khononov 2021, ch. 9)
- **Process manager** - Intent: a stateful coordinator that drives a more complex, branching business process (more decision logic than a saga). When: a workflow needs explicit state and routing decisions. Trade-offs: more machinery than a simple event-reaction saga. (Khononov 2021, ch. 9; Vernon 2013, ch. 4)
- **Model translation (ACL/OHS implementation)** - Intent: translate between a context's model and an external one, statelessly (proxy/API gateway) or statefully (aggregating/unifying sources with its own storage). When: implementing anticorruption layers or open-host services. Trade-offs: stateless is cheap and on-the-fly; stateful needs persistence but handles batching and multi-source unification. (Khononov 2021, ch. 9)

## Principles & best practices
- Make the code speak the ubiquitous language; aggregate names, methods, and domain events all use business terms. (Khononov 2021, ch. 6; Vernon 2013, ch. 10)
- Keep the domain model free of infrastructure (plain old objects); push persistence to the edge via ports/repositories.
- **Model true invariants in consistency boundaries.** Cluster into an aggregate only what must be transactionally consistent. (Vernon 2013, ch. 10)
- **Design small aggregates** (root plus the minimum attributes/value objects needed for its invariants). Large aggregates wreck performance and scalability. (Vernon 2013, ch. 10)
- **Reference other aggregates by identity**, not by direct object reference. (Vernon 2013, ch. 10)
- **Use eventual consistency outside the aggregate boundary**, driven by domain events. (Vernon 2013, ch. 10)
- Encapsulate invariants to reduce a system's degrees of freedom, which is the real measure of complexity. (Khononov 2021, ch. 6)
- Publish domain events reliably with the Outbox pattern; never publish directly from inside the aggregate or before the transaction commits. (Khononov 2021, ch. 9)

## Anti-patterns & pitfalls
- **Anemic domain model** implied as the failure of putting logic in services and treating entities as data bags; behavior belongs with the data it governs.
- **Large-cluster aggregates** built for compositional convenience or false invariants: they load thousands of objects for trivial operations and never scale.
- **Multi-aggregate transactions** as a habit: a signal of wrong aggregate boundaries.
- **Primitive obsession**: representing money, phone numbers, country codes as raw strings/ints, scattering validation and inviting bugs. Use value objects.
- **Publishing domain events from inside the aggregate** or after commit without an outbox: events can be lost or contradict committed state.
- **Forcing one architecture across a whole bounded context** that spans multiple subdomain types, creating accidental complexity.
- **Domain services as a dumping ground** for logic that should live in aggregates or value objects.

## Decision guidance
- **Choose the business-logic pattern (Khononov's decision tree):** money/audit/deep-analytics needed → event-sourced domain model; otherwise complex logic → domain model; otherwise complex data structures → active record; otherwise → transaction script. The chosen pattern is also a check on your subdomain classification (a "core" subdomain that only needs a transaction script may not be core).
- **Choose the architecture from the logic pattern:** event-sourced domain model → CQRS (required); domain model → ports and adapters; active record → layered + application/service layer; transaction script → minimal layered. CQRS is independently useful whenever data needs multiple persistent models.
- **Choose the testing strategy from the patterns:** domain model / event-sourced → testing pyramid (aggregates and value objects are ideal units); active record → testing diamond (emphasis on integration across service and business layers); transaction script → reversed pyramid (emphasis on end-to-end). (Khononov 2021, ch. 10)
- **Decide consistency** with "ask whose job it is": the user's job → transactional inside an aggregate; another user's or the system's job → eventual consistency via domain events. (Vernon 2013, ch. 10)
- **Size aggregates** by true invariants and small footprint; reference others by identity; keep everything eventually consistent across boundaries.
- **Reach for advanced patterns only when needed.** Prefer simple tools and escalate to domain model, event sourcing, and CQRS when the domain genuinely demands it; heuristics, not hard rules.

## Source attribution
- Business-logic patterns (transaction script, active record, domain model, event-sourced domain model) and the selection decision trees: Khononov 2021, ch. 5–7, ch. 10.
- Value objects, entities, aggregates, aggregate root, domain events, domain services, degrees-of-freedom view of complexity: Khononov 2021, ch. 6; Vernon 2013, ch. 5–8, ch. 10.
- Factories and repositories: Vernon 2013, ch. 11–12.
- Application/service layer, modules: Vernon 2013, ch. 9, ch. 14; Khononov 2021, ch. 6, ch. 8.
- Layered architecture, ports and adapters (hexagonal/onion/clean), CQRS, architectural scope/slices: Khononov 2021, ch. 8; Vernon 2013, ch. 4.
- Event sourcing: Khononov 2021, ch. 7; Vernon 2013, ch. 4 and Appendix A.
- Aggregate rules of thumb (true invariants, small aggregates, reference by identity, eventual consistency) and "ask whose job it is": Vernon 2013, ch. 10.
- Outbox, saga, process manager, stateless/stateful model translation: Khononov 2021, ch. 9; Vernon 2013, ch. 4.
- Testing strategy heuristics: Khononov 2021, ch. 10.

## Recent developments (2024–2026)
- **Functional core / imperative shell is reshaping tactical implementation.** A 2025 strand of "lean, functional event sourcing" keeps command handlers pure (a handler returns `Event[]` and performs no I/O) and pushes side effects to the edges, making aggregates trivially testable and reducing concurrency bugs. This pairs naturally with the file's `f(state, command) = events` framing and with ports-and-adapters. (ricofritzsche.me, "Beyond Aggregates: Lean, Functional Event Sourcing", 2025, https://ricofritzsche.me/functional-event-sourcing/)
- **Vertical Slice Architecture is a popular packaging of tactical DDD.** Each business capability becomes an isolated slice bundling commands, queries, domain logic, persistence config, and validators, aligning code with behavior rather than horizontal layers and shrinking the change blast radius; it is frequently combined with aggregates and event sourcing in .NET and TypeScript. (Medium, "Vertical Slice Aggregates", 2025, https://medium.com/@vahidbakhtiaryinfo/vertical-slice-aggregates-a-real-alternative-to-clean-architecture-in-net-03dd9e03d68d)
- **Tactical DDD remains the standard recipe for service boundaries.** Microsoft's Azure Architecture Center continues to recommend aggregates, domain events, and bounded contexts to model microservices, and recent studies report measurable scalability/maintainability gains from these patterns. (Microsoft Learn, "Use Tactical DDD to Design Microservices", 2024-2025, https://learn.microsoft.com/en-us/azure/architecture/microservices/model/tactical-ddd)
- **LLMs generate first-draft tactical artifacts.** EventStorming/Domain Storytelling outputs are now fed to LLMs to generate candidate aggregates, commands, value objects, and domain events, with humans curating; this speeds up the move from model to code but does not replace invariant and boundary judgment. (codecentric, 2025, https://www.codecentric.de/en/knowledge-hub/blog/from-stories-to-code-how-domain-storytelling-and-eventstorming-give-llms-the-context-they-need)
- **Small-aggregate and eventual-consistency discipline is unchanged.** The rules of thumb (model true invariants, design small aggregates, reference by identity, eventual consistency across boundaries, outbox for reliable publishing) saw no revision; the period's change is in code organization (slices, functional core) and AI-assisted authoring.
