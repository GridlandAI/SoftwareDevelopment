# Enterprise Application Patterns (PoEAA)

**Domain:** 06 - Software Design Patterns and DDD
**Primary sources:** Patterns of Enterprise Application Architecture (Martin Fowler, 2002/2012)

## Overview
Enterprise applications are systems dominated by the display, manipulation, and storage of large amounts of often-complex data, with concurrent access by many users and integration with other enterprise systems. Martin Fowler's *Patterns of Enterprise Application Architecture* (PoEAA) catalogs the recurring solutions that arise when you build such systems, organized by the layer of the application in which they live: domain logic, data source / object-relational mapping, web presentation, concurrency, session state, and distribution. The book is structured as a set of "narratives" (conceptual chapters that frame the trade-offs in each area) followed by a reference catalog of roughly 50 patterns. These patterns are the vocabulary underneath most ORMs (Hibernate, Entity Framework, ActiveRecord, Rails) and web frameworks (Spring MVC, ASP.NET, Django), so understanding them lets an agent reason about what a framework is doing and when its defaults break down.

The central organizing idea is **layering**: separating an application into presentation, domain (business) logic, and data source, with each layer depending only downward. The hardest and most consequential decisions are how rich the domain logic is and how it maps to a relational database, because those choices are far-reaching and hard to refactor later. Most other choices (web view style, session storage, concurrency control) follow from the complexity of the domain logic and the scale of concurrent use.

## Core concepts

### The three principal layers
- **Presentation** - handling interaction between the user (or another system) and the software: HTTP requests, HTML, rich-client UI, or a remote API.
- **Domain (business) logic** - the work the application exists to do: validations, calculations, and the rules of the business.
- **Data source** - communication with databases, messaging, and other infrastructure.
Layers are logical; tiers are physical. You decide separately where each layer runs. (Fowler 2002, ch. 1)

### Domain logic complexity drives everything
Fowler's recurring decision graph plots domain-logic complexity against effort. Simple logic favors procedural styles (Transaction Script, Table Module) with simple data access. As complexity grows, those styles hit a wall where each new feature gets exponentially harder, and a behavior-rich Domain Model (with a sophisticated Data Mapper layer) pays back its higher fixed cost. The crossover point cannot be measured objectively; it is an experienced judgment call. (Fowler 2002, ch. 2)

### The object-relational impedance mismatch
Objects use references and multivalued collections; relations use foreign keys and single-valued, normalized columns. Bridging the two is the dominant technical problem of enterprise apps and splits into a **structural** problem (how tables map to objects: identity, foreign keys, associations, inheritance), a **behavioral** problem (how objects load and save themselves, track changes, and avoid loading the same row twice), and a **metadata** problem (driving the mapping from declarative data rather than hand-written code). (Fowler 2002, ch. 3)

### Concurrency: system vs business transactions
A **system transaction** (ACID, RDBMS-backed) is short and well understood. A **business transaction** is a logical unit of work from the user's view that spans multiple requests and therefore multiple system transactions. Holding a single long system transaction across a business transaction does not scale, so you must enforce ACID-like properties yourself across short transactions: this is **offline concurrency**. The choice between optimistic (conflict detection at commit) and pessimistic (conflict prevention via locks) is not purely technical; it shapes the user experience and needs domain input. (Fowler 2002, ch. 5)

## Frameworks & models

### Domain logic organization (ch. 2)
Three mutually-non-exclusive styles plus an optional service layer:
- **Transaction Script** - one procedure per business action. Simple, obvious transaction boundaries, fits Row/Table Data Gateway. Duplicates code as logic grows.
- **Domain Model** - a web of behavior-rich objects, roughly one instance per database row, organized around domain nouns. Handles complexity well via inheritance, strategies, and other OO patterns; requires a sophisticated data source (usually Data Mapper) and a paradigm shift.
- **Table Module** - a single object per table operating over a Record Set. A middle ground; fits Record-Set-centric platforms (classic .NET).
- **Service Layer** - a use-case-oriented API over Domain Model / Table Module, a good home for transaction control and security. Fowler prefers it thin (or absent), warns the "controller-entity" mix encourages duplication.

### Mapping to relational databases (ch. 3)
- Architectural choices (per primary persistence): Table Data Gateway, Row Data Gateway, Active Record, Data Mapper.
- Behavioral patterns: Unit of Work (change tracking and commit ordering), Identity Map (one in-memory object per row), Lazy Load (defer fetching).
- Structural patterns: Identity Field, Foreign Key Mapping, Association Table Mapping, Dependent Mapping, Embedded Value, Serialized LOB, and three inheritance strategies (Single Table, Class Table, Concrete Table) plus Inheritance Mappers.
- Metadata Mapping plus Query Object and Repository to drive and abstract queries.

### Web presentation (ch. 4)
- **MVC** with an *input controller* separating request interpretation from response rendering and from the model.
- Input controller styles: **Page Controller** (one per page/action) vs **Front Controller** (one handler for all requests, interprets URL).
- View styles: **Template View** (markers in a page) vs **Transform View** (transform model data, e.g., XSLT); either can be made a **Two Step View** (logical screen then rendering) for site-wide consistency.
- **Application Controller** when the machine controls screen flow (wizards); not needed when the user controls navigation.

### Offline concurrency control (ch. 5, 16)
Prefer leaving concurrency to the transaction manager (keep business transactions inside one request, or accept long transactions if scale permits). When you cannot, use Optimistic Offline Lock first (better liveness, easier), Pessimistic Offline Lock when conflicts are painful (early failure), and reduce bookkeeping with Coarse-Grained Lock and Implicit Lock.

### Distribution (ch. 7)
**First Law of Distributed Object Design: Don't distribute your objects.** In-process calls are orders of magnitude faster than cross-process or cross-machine calls, so fine-grained interfaces must not cross process boundaries. Prefer clustering (run copies of the whole app per node) over splitting objects across nodes. Where you genuinely must distribute (client/server, app server/DB, packages), put coarse-grained Remote Facades and Data Transfer Objects at the boundary while keeping fine-grained objects inside.

### Session state (ch. 6, 17)
Stateless servers scale and recover more easily, but many sessions are inherently stateful (multi-request business transactions). Choose where to keep that state: Client Session State, Server Session State, or Database Session State, trading off scalability, freshness, and complexity.

## Patterns
For each: name, intent, when to use, trade-offs. (Also appended to PATTERN-CATALOG.md.)

### Domain logic
- **Transaction Script** - Intent: organize business logic as one procedure per user action. When: simple domain logic, small apps, or logic that is naturally procedural. Trade-offs: easiest model and transaction boundaries; duplicates code and tangles as complexity rises.
- **Domain Model** - Intent: an object model of interconnected objects combining behavior and data. When: complex, evolving business rules and invariants. Trade-offs: scales with complexity and enables OO techniques; high learning cost and demands a sophisticated O/R layer (Data Mapper).
- **Table Module** - Intent: a single class per table operating on a Record Set. When: Record-Set-centric platforms and moderately complex logic. Trade-offs: more structure than Transaction Script and great tooling fit; cannot use per-instance OO techniques like inheritance/strategies.
- **Service Layer** - Intent: a use-case-oriented API over the domain that hosts transaction and security policy. When: a Domain Model or Table Module needs a clear application boundary. Trade-offs: clean API and cross-cutting home; keep it thin or business logic leaks into it.

### Data source architecture
- **Table Data Gateway** - Intent: one object per table holding all its SQL, returning Record Sets. When: with Table Module or stored-procedure-centric access. Trade-offs: simple SQL isolation; not object-oriented per row.
- **Row Data Gateway** - Intent: one object per row that mirrors a database row with no domain logic. When: with Transaction Script to isolate DB access. Trade-offs: clean separation; finders are awkward, can stop substitution for testing.
- **Active Record** - Intent: a domain object that wraps a row and carries its own CRUD plus simple domain logic. When: domain logic is simple and maps one-to-one to tables. Trade-offs: minimal indirection and very productive; breaks down as the model diverges from the schema or needs inheritance.
- **Data Mapper** - Intent: a separate layer that moves data between objects and the database, keeping both ignorant of each other. When: a rich Domain Model. Trade-offs: full isolation and independent evolution of model and schema; the most complex option (usually buy an ORM rather than build).

### Object-relational behavioral
- **Unit of Work** - Intent: track every object read and changed and coordinate one ordered, atomic write at commit. When: nontrivial database interaction with a Domain Model. Trade-offs: removes scattered save calls and solves commit ordering/concurrency; central mechanism to build or buy.
- **Identity Map** - Intent: keep a map of loaded rows so each row yields exactly one in-memory object. When: any session that may load the same row twice. Trade-offs: prevents inconsistent duplicate objects and doubles as a cache; primary purpose is correctness, not speed.
- **Lazy Load** - Intent: a placeholder that fetches an object/collection only when first accessed. When: large object graphs where eager loading is wasteful. Trade-offs: bounds initial reads; risks chatty queries (N+1) and surprises during navigation.

### Object-relational structural
- **Identity Field** - Intent: store the database key in the object to map references to rows. When: any persisted entity. Trade-offs: tedious but foundational for all reference mapping.
- **Foreign Key Mapping** - Intent: map an inter-object reference (or collection) to a foreign key. When: one-to-one and one-to-many associations. Trade-offs: standard technique; collection handling requires detecting adds/removes.
- **Association Table Mapping** - Intent: map a many-to-many association via a dedicated link table. When: collections on both ends. Trade-offs: the only relational option for many-to-many; an extra table to maintain.
- **Dependent Mapping** - Intent: have one class handle the persistence of another that is only used within its scope. When: child objects never referenced outside their owner. Trade-offs: simplifies mapping; only valid for strictly owned objects.
- **Embedded Value** - Intent: map a small value object's fields into the owner's table. When: value objects like money or date range. Trade-offs: no extra table or Identity Map needed; cannot be queried independently.
- **Serialized LOB** - Intent: save a cluster of objects as a single large column (XML/blob). When: relatively isolated hierarchic structures (org charts, BOMs). Trade-offs: cuts round-trips; opaque to SQL queries, so use only when you will not query the parts.
- **Single Table Inheritance** - Intent: one table for the whole class hierarchy. When: hierarchies with few type-specific fields. Trade-offs: simplest, no joins; wasted nullable columns and a wide table prone to lock contention.
- **Class Table Inheritance** - Intent: one table per class in the hierarchy. When: normalized schemas mirroring the class structure. Trade-offs: clean and DBA-friendly; loads/saves require multiple joins.
- **Concrete Table Inheritance** - Intent: one table per concrete class. When: querying mostly by concrete type. Trade-offs: no joins per object; schema changes ripple and abstract-type queries are hard.
- **Inheritance Mappers** - Intent: a structure of mappers to handle inheritance mapping consistently. When: any of the inheritance strategies above. Trade-offs: organizes mapping code; added abstraction.

### Object-relational metadata mapping
- **Metadata Mapping** - Intent: hold mapping details as data and generate the mapping behavior from it. When: large systems with many similar mappings. Trade-offs: removes repetitive mapping code (the basis of modern ORMs); harder to debug generated behavior.
- **Query Object** - Intent: an object representing a query, built from domain terms and translated to SQL. When: complex or composable queries decoupled from the schema. Trade-offs: queries in the ubiquitous language and DB-independent; nontrivial to build (often provided by the ORM).
- **Repository** - Intent: a collection-like interface mediating between the domain and data mapping, queried with Query Objects/criteria. When: a Domain Model wanting persistence ignorance. Trade-offs: clean domain-facing access; another layer over the mapper.

### Web presentation
- **Model View Controller** - Intent: separate model, view, and input controller so presentation and domain vary independently. When: virtually all interactive presentation. Trade-offs: testability and multiple presentations; more moving parts and a confusingly overloaded "controller" term.
- **Page Controller** - Intent: one input controller per page or action. When: most page-oriented sites. Trade-offs: simple and intuitive; logic can duplicate across pages.
- **Front Controller** - Intent: a single handler that interprets all requests and dispatches commands. When: centralizing HTTP handling and cross-cutting concerns. Trade-offs: avoids per-action server config and centralizes policy; more complex than Page Controller.
- **Template View** - Intent: render a page by embedding markers/scriptlets in HTML. When: response with little branching logic; designer-friendly markup. Trade-offs: natural for designers; tempts unmaintainable logic in the page (use helpers).
- **Transform View** - Intent: render by transforming model data element by element (e.g., XSLT). When: data already in or convertible to XML. Trade-offs: clean separation of logic from markup; transform languages are awkward and another skill.
- **Two Step View** - Intent: produce a logical screen, then render it to the target format in a second, shared stage. When: site-wide consistent look or multiple front-ends/devices over a common logical screen. Trade-offs: global look changes in one place; needs a stable shared logical screen.
- **Application Controller** - Intent: a layer that decides screen flow and navigation. When: the machine drives the order of screens (wizards, complex flows). Trade-offs: reusable flow across presentations; unnecessary and obstructive when the user controls navigation.

### Distribution
- **Remote Facade** - Intent: a coarse-grained facade over fine-grained objects, existing only to provide a remote interface. When: a process/network boundary must be crossed. Trade-offs: minimizes expensive remote calls and keeps the cost explicit; only delegation, no logic.
- **Data Transfer Object** - Intent: a serializable object carrying a batch of data across the wire in one round-trip. When: returning/accepting aggregated data over a remote interface. Trade-offs: collapses many fine-grained reads into one transfer; references only other DTOs and primitives, adds mapping code.

### Offline concurrency
- **Optimistic Offline Lock** - Intent: detect conflicts at commit via a version marker on each record. When: conflicts are rare or cheap to redo; liveness matters. Trade-offs: best concurrency and easiest to build; failure is discovered late, after work is done.
- **Pessimistic Offline Lock** - Intent: prevent conflicts by acquiring locks before editing. When: conflicts are likely and painful (expensive lost work). Trade-offs: early failure and protected work; reduces concurrency, harder to implement, deadlock risk.
- **Coarse-Grained Lock** - Intent: lock a group of related objects with a single lock. When: aggregates/object clusters change together. Trade-offs: far less locking bookkeeping; needs domain knowledge to pick the group.
- **Implicit Lock** - Intent: acquire locks automatically in framework code so application developers cannot forget. When: lock discipline must be enforced across a team. Trade-offs: prevents a whole bug class; hides behavior, so the framework must be trusted.

### Session state
- **Client Session State** - Intent: store session data on the client (cookie, hidden field, URL). When: small state, maximal server scalability/statelessness. Trade-offs: best scalability and failover; size limits and security/tampering concerns.
- **Server Session State** - Intent: keep session state in server memory keyed by session id. When: convenient, rich session objects. Trade-offs: simplest programming model; hurts scalability/failover and consumes memory.
- **Database Session State** - Intent: persist session state in the database between requests. When: need durability and statelessness of the app server. Trade-offs: scalable and crash-resilient; extra DB load and serialization/cleanup cost.

### Base patterns (selected)
- **Gateway** - Intent: an object that encapsulates access to an external system or resource behind a simple interface. When: wrapping APIs, services, or tables treated as externals. Trade-offs: isolates and makes external access substitutable (Service Stub); a thin extra layer.
- **Money** - Intent: a value object representing an amount and currency with correct arithmetic and rounding. When: any monetary value. Trade-offs: eliminates a major class of currency/rounding bugs; a small type to introduce everywhere.
- **Special Case** - Intent: a subclass providing special behavior for particular cases (e.g., a Null Object). When: replacing repeated null/edge checks. Trade-offs: removes conditional clutter; one more class per case.
- **Layer Supertype** - Intent: a common superclass for all objects in a layer. When: shared behavior across a layer's types. Trade-offs: removes duplication; do not let it become a god-class table that triggers lock escalation.
- **Registry** - Intent: a well-known object others use to find common objects/services. When: global access points (configuration, caches). Trade-offs: convenient lookup; a form of global state, must be thread-safe.
- **Separated Interface** - Intent: define an interface in one package and its implementation in another to control dependencies. When: decoupling clients from implementations across layers. Trade-offs: enables dependency inversion; more packages/indirection.
- **Service Stub** - Intent: a stand-in for a problematic service to enable testing. When: tests must run without a slow/unreliable external. Trade-offs: fast, deterministic tests; must keep the stub faithful.

## Principles & best practices
- Layer the application (presentation / domain / data source) and let dependencies point only downward. Decide tier placement separately. (ch. 1)
- Match domain-logic style to complexity: start with Transaction Script/Table Module, move to Domain Model when complexity warrants; refactoring Transaction Script -> Domain Model is reasonable, the reverse rarely is. (ch. 2)
- Pick one primary persistence mechanism (Active Record or Data Mapper) and do not mix them as the main strategy; Gateways may still wrap external tables/services. (ch. 3)
- A rich Domain Model needs Unit of Work, Identity Map, and Lazy Load to manage behavior; buy an ORM rather than build a Data Mapper by hand, but learn the patterns to use the ORM well. (ch. 3)
- Keep concurrency control with the transaction manager when possible; fit business transactions into one request, or accept long transactions only when scale allows. (ch. 5)
- Don't distribute objects: cluster instead; place Remote Facades and DTOs only at genuine process boundaries; prefer coarse-grained interfaces remotely and fine-grained ones locally. (ch. 7)
- Prefer statelessness, but recognize inherently stateful sessions and choose deliberately where session state lives. (ch. 6)
- Adopt continuous integration, test-driven development, and refactoring so architectural decisions remain changeable. (ch. 8)

## Anti-patterns & pitfalls
- **Distributing fine-grained objects across nodes "for performance,"** which cripples performance with remote-call overhead and forces awkward coarse-grained interfaces everywhere.
- **Using a Gateway as the primary persistence for a rich Domain Model**, leaving the model coupled to the schema with messy transformation code.
- **Mixing Active Record and Data Mapper** as the main persistence strategy.
- **Putting full programming logic into Template Views** (server pages), producing unmaintainable presentation code.
- **A global "object" table at the Layer Supertype level**, which invites lock escalation and shuts everyone out of the database.
- **Holding long system transactions** across multi-request business transactions, turning the database into a bottleneck.
- **Choosing serializable isolation everywhere** without weighing the liveness cost, or conversely ignoring inconsistent-read/phantom risks at lower isolation levels.
- **Treating concurrency control as a post-requirements technical detail** instead of a UX decision needing domain input.

## Decision guidance
- **Domain logic:** estimate complexity. Low and procedural -> Transaction Script (+ Row/Table Data Gateway). Low but Record-Set-centric platform -> Table Module (+ Table Data Gateway). High/evolving -> Domain Model (+ Data Mapper, Unit of Work, Identity Map, Lazy Load). Add a thin Service Layer when you need a clear application API or transaction/security home.
- **Persistence:** simple model close to schema -> Active Record. Rich model diverging from schema, or needing inheritance/testing in isolation -> Data Mapper / Repository with Metadata Mapping.
- **Inheritance mapping:** few type-specific fields -> Single Table; want normalization/DBA clarity -> Class Table; query mostly by concrete type -> Concrete Table.
- **Web input controller:** page-oriented -> Page Controller; centralized cross-cutting handling -> Front Controller. **View:** designer-friendly markup -> Template View; XML data/transformation pipeline -> Transform View; site-wide consistent look or multi-device -> add Two Step View. Add an Application Controller only when the machine controls screen flow.
- **Concurrency:** keep it in the transaction manager if you can; otherwise Optimistic Offline Lock first, Pessimistic Offline Lock when conflicts are painful, and Coarse-Grained / Implicit Lock to cut bookkeeping. Tune isolation per transaction, balancing correctness against liveness.
- **Distribution:** default to a single process and clustering; introduce Remote Facade + DTO only at real boundaries; prefer messaging/async over synchronous RPC where possible.
- **Session state:** maximize scalability with Client Session State; default to Server Session State for convenience; use Database Session State when durability and app-server statelessness matter.

## Source attribution
- Layering and the three principal layers, tiers vs layers, putting it all together: Fowler 2002, ch. 1, ch. 8.
- Domain logic styles (Transaction Script, Domain Model, Table Module, Service Layer) and the complexity-vs-effort decision: Fowler 2002, ch. 2, ch. 9.
- Mapping to relational databases, the structural/behavioral/metadata split, finders, Unit of Work, Identity Map, Lazy Load rationale: Fowler 2002, ch. 3, ch. 10–13.
- Web presentation (MVC, Page/Front Controller, Template/Transform/Two Step View, Application Controller): Fowler 2002, ch. 4, ch. 14.
- Concurrency (system vs business transactions, ACID, isolation levels, optimistic/pessimistic, deadlocks, application-server concurrency) and offline-lock patterns: Fowler 2002, ch. 5, ch. 16.
- Session state and its patterns: Fowler 2002, ch. 6, ch. 17.
- Distribution strategies, First Law of Distributed Object Design, Remote Facade, DTO: Fowler 2002, ch. 7, ch. 15.
- Base patterns (Gateway, Money, Special Case, Layer Supertype, Registry, Separated Interface, Service Stub, Value Object, Record Set): Fowler 2002, ch. 18.

## Recent developments (2024–2026)
- **Modular monolith resurgence vindicates the layering/Service Layer core.** 2025 guidance strongly favors a modular monolith (single deployable, well-bounded internal modules) as the default, splitting into services only when the monolith genuinely hurts, echoing Fowler's "Monolith First" and the First Law of Distributed Object Design ("Don't distribute your objects"). Reported wins include Amazon Prime Video's move from microservices back toward a consolidated service for a ~90% cost reduction. (javacodegeeks.com, "Microservices vs. Modular Monoliths in 2025", 2025, https://www.javacodegeeks.com/2025/12/microservices-vs-modular-monoliths-in-2025-when-each-approach-wins.html; softwareseni.com, 2025, https://www.softwareseni.com/understanding-modern-software-architecture-from-microservices-consolidation-to-modular-monoliths/)
- **Team-size economics now quantified.** Recent surveys report microservice benefits appear mainly above ~10 developers, with monoliths outperforming below that threshold, giving a concrete decision input alongside Fowler's domain-complexity crossover. (florian-kraemer.net, "When to build Microservices or a Modular Monolith?", 2025, https://florian-kraemer.net/software-architecture/2025/10/20/Microservices-are-rarely-what-you-need.html)
- **Strangler Fig remains the canonical modernization path.** Fowler's Strangler pattern continues to be the recommended way to evolve legacy enterprise apps incrementally toward new architectures, including monolith-to-modular and selective service extraction. (paradigmadigital.com, "Architecture Patterns: From Monolith to Microservices", 2025, https://en.paradigmadigital.com/dev/architecture-patterns-from-monolith-to-microservices/)
- **The O/R mapping patterns still underpin every modern ORM.** Unit of Work, Identity Map, Lazy Load, Data Mapper, Active Record, Metadata Mapping, Query Object, and Repository remain the vocabulary behind Hibernate, Entity Framework, ActiveRecord, and their successors; no new foundational O/R pattern displaced them in this period.
- **PoEAA endures as reference rather than evolving text.** The 2002 catalog received no new edition; its value in 2024-2026 is as the stable vocabulary for reasoning about ORM defaults, session state, concurrency, and distribution, increasingly read alongside DDD tactical patterns and the modular-monolith literature above.
