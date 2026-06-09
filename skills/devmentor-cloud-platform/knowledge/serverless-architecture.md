# Serverless Architecture

**Domain:** 09 - Cloud Infrastructure and Platform
**Primary sources:** Software Architecture Patterns for Serverless Systems (Gilbert, Lavi, & Price, 2024); Cloud-Native Application Architecture: Microservice (FreeWheel Biz-UI Team, 2024, Chapter 6); Cloud-Native Applications on Microsoft Azure (Deshmukh, Malviya, & Mohammed, 2026)

## Overview

Serverless is a cloud model in which the provider fully manages the servers, capacity, scaling, and availability, so developers care only about code and business logic. The provider scales the workload up and down automatically (often to zero) and bills only for actual usage. The CNCF white paper divides serverless into two categories: **Backend-as-a-Service (BaaS)**, where providers expose ready-made backend capabilities (auth, file storage, push messaging) through unified APIs and SDKs, and **Function-as-a-Service (FaaS)**, where a developer packages a function, declares the events that trigger it, and the platform executes, scales, and tears it down per invocation. AWS Lambda (2014) was the first FaaS; Azure Functions, Google Cloud Functions, and IBM OpenWhisk followed. (FreeWheel; Gilbert et al.)

Serverless matters across the SDLC because it makes autonomous, full-stack teams practical. Teams self-service their resources instead of waiting on central infrastructure teams; the resources are highly available and highly observable out of the box; and the decision is disposable, so a team can start serverless-first and move to self-managed technology later only if metrics justify it (Lean's "defer commitment, create knowledge"). Gilbert et al. argue that the real value of serverless is reduced lead time and risk: it lets teams experiment, fail forward fast, and discover business value without the months-long learning curve of safely operating servers, queues, and databases at scale. This file covers serverless and FaaS patterns, event-first/event-driven design, function composition, the strangler and autonomous-service patterns, data management, observability and security, the cost model and cold starts, and when serverless fits.

## Core concepts

- **Serverless-first.** Default to a managed serverless option for each service; it requires little up-front investment, the decision is made and unmade per service, and it acts as a safety net while you learn whether a feature delivers value. Avoid emotional attachment to self-managed technology you invested in learning. (Gilbert et al.)
- **Autonomous service.** A self-contained service that owns all the resources it needs (its own data store, its own functions) so it keeps functioning even when other services cannot. Autonomous services are the unit of independent change and deployment. (Gilbert et al.)
- **Autonomous subsystem.** A bounded context grouping cohesive autonomous services around a single dominant actor or business capability, housed in its own cloud account, communicating with other subsystems only through external (integration) domain events. (Gilbert et al.)
- **Bulkhead.** A construct placed between components that contains failure (a ship's watertight compartment). In this architecture the bulkheads are highly available, fully managed serverless resources: the event hub is the outbound bulkhead protecting upstream services; a service's own data store is the inbound bulkhead protecting it from upstream; separate cloud accounts are subsystem-level bulkheads. (Gilbert et al.)
- **Event-first.** Treat domain events as the primary contract and integration mechanism. Subsystems connect through a central event hub in a simple fractal topology that repeats from service-to-service up to subsystem-to-subsystem, letting arbitrarily complex systems evolve by simply adding consumers. (Gilbert et al.)
- **Event lake.** The durable store of all events: a record of truth, a complete audit log, a source of metrics, and a means to replay events to repair, seed, or investigate services. (Gilbert et al.)
- **Idempotence and order tolerance.** Because exactly-once and in-order delivery are unrealistic, services must be idempotent (processing the same event many times updates the system once) and order-tolerant (ready to receive events out of order and sort them out). Achieved with deterministic identifiers (client-generated UUIDs or content-derived keys), inverse optimistic locking, and immutable event triggers. (Gilbert et al.)
- **Data gravity.** The tendency of accumulated, over-related data to lock an architecture in place and crush agility. Serverless polyglot persistence plus event-driven movement of data through its life cycle "turns the cloud into the database" and liberates data. (Gilbert et al.)
- **Cold start.** The latency incurred when the FaaS platform must initialize a new function instance (download bundle, start runtime) because of a long idle period or a burst of concurrency. (Gilbert et al.; FreeWheel)

## Frameworks & models

### The data life cycle (four phases)

Different actors interact with the same logical data at different times for different purposes; each phase has its own storage, access, and archiving needs. Gilbert et al. implement a separate autonomous service per actor/aggregate/phase combination and pick the best database for each.

1. **Create** - optimize for writes. Generated/integrated data is written to a stream (stream-first event sourcing, e.g., Kinesis). Manual entry uses a normalized store (DynamoDB, Aurora) with Change Data Capture so the database-first event-sourcing variant can publish events.
2. **Use** - many downstream services materialize their own lean read views from upstream events, each optimized for its queries.
3. **Analyze** - analytics/OLAP-style consumption, decoupled from transactional load.
4. **Archive** - records-management and retention, with proactive real-time archiving so transactional services stay lean.

Designing around verbs (the data life cycle) instead of nouns (object-oriented aggregates) decouples life-cycle phases, reduces data gravity, and lets each service keep its data lean.

### The three autonomous-service patterns

Every autonomous service falls into one of three higher-order patterns, each responsible to a different category of actor (Gilbert et al.):

- **Backend for Frontend (BFF)** - serves end-user actors and their UI; covered in Patterns with its sub-variations.
- **External Service Gateway (ESG)** - bridges to external systems (third-party, legacy, sister subsystems) as an anti-corruption layer; covered in Patterns.
- **Control Service** - sits between boundary services as a mediator, embodying organizational policy and orchestrating complex business processes; covered in Patterns.

### Anatomy of an autonomous service

A service is assembled from serverless functions wired to events and data: a **listener** function consumes domain events and materializes a lean local view; a **query** function serves reads from that view; a **command** function performs writes; a **trigger** function produces outbound domain events recording outcomes. Business logic lives in plain **model** classes; **connector** classes encapsulate external dependencies; handlers glue them to the runtime (e.g., an Apollo GraphQL handler in a Lambda). This keeps the domain decoupled from the execution environment and testable in isolation. (Gilbert et al.)

### Choreography vs. orchestration (control flow)

- **Choreography** - no central coordinator; upstream services emit events and downstream services react. Best for data-synchronization and observation flows; services stay decoupled and you can add/remove consumers freely. It works for simple control flow but does not scale to intricate processes, where the logic spreads across services and becomes hard to trace, debug, and maintain.
- **Orchestration via control services** - consolidate complex control-flow logic upstream in a dedicated mediator that consumes lower-order events, evaluates rules, and produces higher-order events (business-process orchestration, sagas, complex event processing, ML-driven control). Start with choreography and evolve to control services as the optimal solution becomes clear; mixtures are common. (Gilbert et al.)

### Observability methods (Gilbert et al.)

Serverless emits rich metrics cheaply. Turn them into action with two complementary methods: the **USE method** for resource metrics (Utilization, Saturation, Errors) and the **RED method** for work metrics (Rate, Errors, Duration). Add real user monitoring and synthetics at the frontend. Observability enables fail-forward-fast confidence, continuous governance (monitoring config changes for compliance), worth-based/FinOps tuning, and experiment evaluation.

### The serverless cost model (Gilbert et al.; FreeWheel)

- Functions are billed in gigabyte-seconds; price scales with allocated memory, but memory also raises CPU and network throughput, so more memory can mean lower total cost and better performance. Start at a moderate default (e.g., 1,024 MB) and tune the most expensive functions from production metrics.
- Serverless databases (e.g., DynamoDB) bill by capacity consumed per request, so keeping materialized views lean cuts both cost and latency. Set a TTL per record to keep tables lean and reduce the impact of upstream changes.
- FaaS bills only when events occur; idle functions cost nothing. This is the core economic advantage over always-on IaaS/PaaS that is billed whether or not it is used. (FreeWheel)

## Patterns

- **Autonomous service** - Intent: a self-contained service owning its own functions and data store so it keeps working when others fail. When to use: as the default unit of an event-first serverless system. Trade-offs: independent deployability and fault isolation vs. data duplication and eventual consistency. (Gilbert et al.)
- **Backend for Frontend (BFF)** - Intent: a dedicated service tailored to one frontend/actor's needs, optimized via CQRS. When to use: divergent client experiences across the data life cycle. Variations Gilbert et al. enumerate: **CRUD** (create-phase master/reference data, system of record), **List-of-values (Lov)** (read-only reference lists for data entry, often cached offline), **Task** (a step in a business process: query the needed data, commands to complete it, a completion event), **Search** (read-only index such as Elasticsearch over upstream data, served via CDN for public data), **Action** (headless: performs commands with all context passed in, retains nothing), **Dashboard/Reporting/Archive** (later life-cycle phases). Trade-offs: per-actor optimization and high read performance vs. more services and duplicated views. (Gilbert et al.)
- **External Service Gateway (ESG) / anti-corruption layer** - Intent: a macro-level ports-and-adapters boundary that bridges domain events to and from external systems (third-party, legacy, sister subsystems, cross-cloud), translating external models to the internal one. When to use: any integration with a system you do not control. Trade-offs: isolation and independent evolution (and LSP-based substitution of one ESG for another) vs. an extra adapter layer per external system. Ingress ESGs adapt inbound external events; egress ESGs decide what events to share. (Gilbert et al.)
- **Control Service (mediator/orchestrator)** - Intent: consolidate intricate control-flow and policy logic in a service between the boundary services. When to use: business processes too complex for choreography (multiple alternate/error paths, saga coordination, CEP, ML control). Trade-offs: clear, traceable control and isolation of volatile policy vs. a potential coordination hotspot. (Gilbert et al.)
- **System-wide Event Sourcing** - Intent: persist state as an append-only stream of events that is the record of truth and supports replay. Variations: **stream-first** (write events directly to a stream for generated/integrated data) and **database-first** (use Change Data Capture on a normalized store to emit events). When to use: audit, replay/repair, seeding new services, high write throughput. Trade-offs: powerful history and decoupling vs. needing projections and event-schema versioning. (Gilbert et al.)
- **System-wide CQRS** - Intent: separate the write model from rebuildable read models (materialized views) synchronized via the event hub; each downstream service projects only the lean fields it needs. When to use: high read-to-write ratios, many distinct views, polyglot per-view storage. Trade-offs: independent scaling, performance, and resilience vs. eventual consistency and projection maintenance. (Gilbert et al.)
- **Inbound / outbound bulkheads** - Intent: use serverless resources (event hub outbound; per-service data store inbound; separate cloud accounts at subsystem level) to buffer changes, cache data, and contain blast radius. When to use: every boundary in the system. Trade-offs: strong fault and security isolation vs. duplicated cross-cutting capabilities across accounts. (Gilbert et al.)
- **Strangler (event-first migration)** - Intent: modernize legacy non-invasively by wrapping it in an ESG anti-corruption layer that turns legacy data-change events into external domain events and consumes domain events back, creating bi-directional sync; build new features around the legacy until it stops emitting events and can be switched off with no cutover. When to use: every legacy modernization where the system must keep running. Trade-offs: continuous value delivery, no big-bang cutover, fully controlled risk vs. running old and new in parallel and a slight headless-mode change to weave legacy UIs into the micro frontend. (Gilbert et al.)
- **Deterministic identifiers for idempotence** - Intent: have the client generate the identifier (V4 UUID for entities to avoid hot partitions, V1 UUID for time-sortable events, or a content-derived key) and use idempotent HTTP PUT, so retries and replays create or update exactly one record. When to use: any at-least-once delivery path. Trade-offs: safe retries/replays vs. the client (or session/local storage) needing to remember the identifier. (Gilbert et al.)
- **Time to live (TTL) for lean data** - Intent: set a per-record TTL so the database auto-expires data past its useful life, keeping tables lean and reducing the cost of upstream schema changes. When to use: services whose data has a bounded useful life. Trade-offs: lower cost and easier evolution vs. care not to remove data so aggressively that idempotence/order tolerance breaks. (Gilbert et al.)
- **Cache-control + CDN** - Intent: set cache-control headers on query responses and serve through a CDN so browsers and the edge reuse responses. When to use: high-traffic read resources. Trade-offs: large cost and latency reduction on cache hits vs. staleness within the max-age window. (Gilbert et al.)

## Principles & best practices

- **Defer commitment; create knowledge.** Start serverless-first to learn fast and cheaply; switch to self-managed only when cost/performance metrics justify it. (Gilbert et al.)
- **Make services idempotent and order-tolerant by design.** Assume at-least-once, out-of-order delivery; never rely on exactly-once. (Gilbert et al.)
- **Keep data lean.** Project only needed fields, set TTLs, and apply the robustness principle (conservative in what you send, liberal in what you receive) so downstream services can ignore irrelevant upstream changes. (Gilbert et al.)
- **Communicate across subsystems only via stable external events.** Internal events can change freely within a subsystem; external (integration) events have stronger backward-compatibility requirements and change slowly, forming a bulkhead. (Gilbert et al.)
- **One account per subsystem per environment.** Controls technical debt, attack surface, resource contention, cost allocation, and blast radius. (Gilbert et al.)
- **Minimize cold starts.** Prefer lightweight scripting runtimes (JavaScript), shrink the bundle (tree-shaking via webpack), avoid heavyweight DI/ORM frameworks that introspect at startup, and raise memory to speed bundle download. (Gilbert et al.)
- **Take control of timeouts and retries.** Set short, deliberate connector and function timeouts so retries happen quickly and users do not wait on default 2-minute network timeouts. (Gilbert et al.)
- **Tune for cost, not just speed.** In serverless, performance tuning directly lowers cost; tune the most expensive functions until further effort costs more than it saves (worth-based/FinOps development). (Gilbert et al.)

## Anti-patterns & pitfalls

- **Lifting a monolith's database into serverless unchanged.** A single over-related OLTP store violates the SRP (more than one actor, more than one reason to change) and maximizes data gravity; split along the data life cycle instead. (Gilbert et al.)
- **Relying on exactly-once or ordered delivery.** These unrealistic expectations cause data discrepancies when (not if) things go wrong. (Gilbert et al.)
- **Choreographing intricate control flow.** Spreading complex, interrelated control logic across services makes the system hard to trace, debug, test, and maintain; consolidate it in a control service. (Gilbert et al.)
- **Weaving the new system invasively into the legacy UI.** Adapter-and-toggle strangler implementations break the Open-Closed Principle by modifying the legacy and still force a risky cutover; use non-invasive event-first migration. (Gilbert et al.)
- **Server-generated identifiers on create paths.** Auto-increment keys make POST non-idempotent, creating duplicate records on retry. (Gilbert et al.)
- **Letting tables grow unbounded.** Without TTLs, cost rises and upstream changes ripple across all historical records. (Gilbert et al.)
- **Heavyweight runtimes and fat bundles.** Inflate cold-start latency. (Gilbert et al.; FreeWheel)
- **Treating the serverless choice as monolithic and permanent.** Forgetting that serverless decisions are disposable and per-service leads to over-commitment. (Gilbert et al.)

## Decision guidance

- **When does serverless fit?** Choose serverless-first for event-driven, spiky, or unpredictable workloads; new features where lead time and learning speed matter; autonomous full-stack teams that should not wait on central infrastructure; and the atomic command/query flows of autonomous services. Reconsider for sustained, high-throughput, latency-critical workloads where always-on capacity may be cheaper or where strict cold-start ceilings cannot be met; but make that call from production cost and performance metrics, not up front. (Gilbert et al.; FreeWheel)
- **FaaS or BaaS?** Use BaaS for standardized backend capabilities (auth, storage, messaging) you should not build; use FaaS for your own event-triggered compute and logic. They compose. (FreeWheel)
- **Choreography or a control service?** Start with choreography for data sync and simple flows; evolve to a control service when alternate/error paths and cross-service coordination make choreography hard to follow. Expect a mixture. (Gilbert et al.)
- **Stream-first or database-first event sourcing?** Stream-first for generated/integrated data written straight to a stream; database-first (via CDC) when users need a normalized store for manual data entry. (Gilbert et al.)
- **How to size a function?** Begin at a moderate memory default; tune only the expensive functions, trading memory against execution time toward lowest total cost. (Gilbert et al.)
- **How to handle legacy?** Wrap it with an ESG anti-corruption layer and apply the event-first strangler so old and new run in parallel and the legacy is switched off only when it stops emitting events; it may even enjoy a long retirement as a reporting/interface pass-through. (Gilbert et al.)

## Source attribution

- **Serverless-first, autonomous services/subsystems, bulkheads, event-first topology and event hub/lake, the BFF/ESG/Control patterns and BFF variations, anatomy of a service, event sourcing (stream-first and database-first via CDC), system-wide CQRS, data life cycle and data gravity, idempotence/order tolerance and deterministic identifiers, TTL and lean data, cache-control/CDN, choreography vs. control services, strangler/event-first migration and headless micro frontends, observability (USE/RED, RUM, synthetics), cost model, function memory tuning, cold starts, timeouts/retries, FinOps tuning:** Gilbert, Lavi, & Price, *Software Architecture Patterns for Serverless Systems* (2024), Chapters 1-8 and 11-13.
- **CNCF definition of serverless, BaaS vs. FaaS split, history (AWS Lambda 2014; Azure/Google/IBM FaaS), serverless advantages (lower cost via usage-based billing, faster iteration), IaaS/PaaS/SaaS evolution leading to serverless:** FreeWheel Biz-UI Team, *Cloud-Native Application Architecture: Microservice* (2024), Chapter 6.
- **Azure Functions as the serverless realization (event triggers, pay-per-use, auto-scale; Functions vs. Logic Apps), serverless within the cloud-native ecosystem:** Deshmukh, Malviya, & Mohammed, *Cloud-Native Applications on Microsoft Azure* (2026), Chapter 1.

## Recent developments (2024–2026)

- WebAssembly (Wasm) emerged as a serious serverless runtime alternative to containers, offering sub-millisecond cold starts, tiny artifacts (often under 50 KB), and far higher density per host than containers. WASI Preview 2 reached stability in 2025, standardizing system interfaces (filesystem, sockets, env) while preserving the Wasm sandbox. (Medium/Kawaldeep Singh, 2025, https://kawaldeepsingh.medium.com/webassemblys-edge-revolution-how-wasm-is-redefining-serverless-computing-in-2025-638e21751386)
- Major providers extended Wasm support: AWS Lambda runs Wasm via its container image format, Azure Functions added experimental Wasm support, and Cloudflare Workers continued running on a Wasm/V8 isolate model. (DZone, 2025, https://dzone.com/articles/webassembly-is-eating-the-cloud-why-devs-should-ca)
- Despite the momentum, Wasm remains early in enterprise adoption: the CNCF 2025 survey found about 65% of organizations have no WebAssembly experience and only ~5% report full deployment, with energy still concentrated on containers, Kubernetes, pipelines, and observability. (CNCF, 2026, https://www.cncf.io/announcements/2026/01/20/kubernetes-established-as-the-de-facto-operating-system-for-ai-as-production-use-hits-82-in-2025-cncf-annual-cloud-native-survey/)
- Serverless became a primary substrate for AI inference and agentic backends, with FaaS and managed event services fronting LLM/RAG flows; the Kubernetes Gateway API added an Inference Extension in 2026 for serving models behind serverless-style routing. (CNCF, 2026, https://www.cncf.io/announcements/2026/03/25/istio-brings-future-ready-service-mesh-to-the-ai-era-with-new-ambient-multicluster-gateway-api-inference-extension-and-more/)
- FinOps for serverless and AI matured: AI spend is now managed by the large majority of FinOps practices, sharpening scrutiny of pay-per-invocation cost models and pushing carbon/GreenOps metrics into the same dashboards. (FinOps Foundation State of FinOps 2025, https://data.finops.org/2025-report/)
