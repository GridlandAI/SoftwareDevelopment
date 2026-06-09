# Event-Driven Architecture

**Domain:** 05 - Architecture Patterns and Styles
**Primary sources:** Implementing Event-Driven Microservices Architecture in .NET (Garverick & McIver, 2023); Practical Microservices Architectural Patterns: Event-Based (Christudas, 2019); Ultimate Microservices with RabbitMQ (Morlion, 2024)

## Overview
Event-driven architecture (EDA) is a style in which components communicate by producing and reacting to events rather than by calling each other directly. An event is an immutable record that something happened in a domain ("OrderPlaced", "PaymentApproved", "TurnstileRotated"). Producers emit events without knowing who, if anyone, will consume them; consumers react at their own pace. This loose, asynchronous coupling is what gives EDA its defining qualities: high scalability, elasticity, responsiveness, and resilience to partial failure.

EDA matters across the SDLC because it changes how teams reason about correctness. The convenient mental model of a single ACID transaction that either fully succeeds or fully fails no longer holds once a business operation spans services that each own their data. Designers must instead embrace eventual consistency, idempotency, compensation, and replayable history. The payoff is systems that scale almost without bound and absorb load spikes and transient faults gracefully; the cost is a more complex programming model and harder testing, debugging, and operational visibility.

## Core concepts

- **Event.** An immutable statement of fact about a past state change. Events carry the information needed to understand what changed; commands (a related concept) express the intent to change state in the future.
- **Producer / Consumer (Publish-Subscribe).** The foundational interaction: any number of producers emit events; any number of consumers subscribe and act. Also called pub/sub. Producers and consumers are decoupled in location, time, and (with transformation) data format. (Garverick & McIver)
- **Eventual consistency (BASE).** State across services converges over time rather than instantly. BASE = Basically Available, Soft state, Eventually consistent. "Basically available" means reads/writes are available but not guaranteed consistent; "soft state" means data may fluctuate before converging; "eventually consistent" means writes will be reflected in subsequent reads given enough time. A well-built system uses both ACID (within a component/partition) and BASE (across components). (Garverick & McIver)
- **Idempotency.** Processing the same event more than once produces the same result as processing it once. Required because at-least-once delivery and retries inevitably create duplicates. Both saga transactions and their compensations must be idempotent. (Garverick & McIver; Christudas)
- **Correlation / causation IDs.** A correlation identifier lets a consumer match a reply to the request that triggered it, and lets operators trace a logical flow across services. (Christudas; EIP heritage)
- **Aggregate / domain entity.** The consistency boundary that owns state and emits events when its state changes. State change inside an aggregate happens in a local ACID transaction; coordination across aggregates uses events. (Christudas)

## Frameworks & models

### EDA topologies: broker vs. mediator
- **Broker topology.** Events flow through a lightweight broker (queue/topic) with no central coordinator. Each consumer decides independently how to react and may emit further events, producing a chain of reactions. Maximizes decoupling and scalability; the overall flow is emergent and harder to see. This is the default in pub/sub messaging systems (Kafka topics, RabbitMQ exchanges).
- **Mediator topology.** A central component (process manager, orchestrator) owns the workflow: it receives the initiating event and issues commands to participants step by step. Easier to understand, monitor, and change the flow; the mediator becomes a coordination hotspot and a potential bottleneck. Christudas's Saga Execution Coordinator (SEC) is a mediator that stays "dumb" by keeping all execution logic in a replayable distributed saga log.

### Four ways to use events (conceptual spectrum)
1. **Event notification.** A thin event ("OrderPlaced", id only) tells others something happened; interested parties call back for details. Lowest coupling and smallest payloads; many follow-up queries and weak traceability of why the system is in a given state.
2. **Event-carried state transfer.** The event carries the data consumers need so they can update their own local copies without calling back. Removes runtime dependency on the producer and improves availability; introduces data duplication and replication lag (eventual consistency). RabbitMQ's headers/topic routing makes selective state transfer practical. (Morlion)
3. **Event sourcing.** The application appends every state change to a log/stream; current state is derived by replaying events. Benefits: full audit trail, point-in-time debugging and "what-if" replay, high write throughput (no CRUD overhead), and natural fit with partitioned, replicated logs like Kafka. Cost: querying requires projections, and schema/versioning of events is a long-term concern. (Garverick & McIver; Christudas)
4. **CQRS (Command Query Responsibility Segregation).** Separate the write model (commands → state change → events) from one or more read models (materialized views/projections). Read and write models can use different schemas and scale independently - typically many read replicas against a controlled number of write nodes to serve high read-to-write ("look-to-book") ratios. Synchronization from write to read happens via the event bus, which makes the read side eventually consistent. CQRS does not require microservices or event sourcing, but the three compose powerfully. (Christudas; Garverick & McIver)

### Choreography vs. orchestration
- **Choreography (broker style).** Each service reacts to events and emits its own; no central conductor. Example: a top-up process emits "TopUpRequested" and stays pending; the payment service reacts at its own pace and emits "PaymentMethodUpdated"; the top-up process reacts to that and completes. Decoupled and extensible (add a new subscriber non-intrusively) but the end-to-end flow is implicit and harder to reason about. (Garverick & McIver)
- **Orchestration (mediator style).** A coordinator drives the steps explicitly. Christudas's distributed saga uses an SEC that executes the saga as a directed acyclic graph (DAG), logging start/end of each transaction and compensation. Clearer control and easier monitoring; the coordinator must itself be made highly available (recoverable from the saga log).

### The saga model for cross-service consistency
A saga replaces a distributed ACID transaction with a sequence of local transactions T1..Tn, each paired with a compensating transaction C1..Cn that semantically undoes it. The saga guarantees either all T's complete, or a prefix completes and is then unwound by the corresponding C's in reverse. Messaging semantics: transactions are "at most once," compensations are "at least once." Transaction and compensation characteristics: both must be idempotent; transactions can abort, compensations cannot; compensations must be commutative (a delayed transaction arriving after its compensation must not re-apply). (Christudas; Garverick & McIver)

## Patterns
*(Also added to PATTERN-CATALOG.md.)*

- **Event Notification** - Intent: signal that something happened with a minimal event, letting consumers fetch detail on demand. When to use: low-coupling notifications where consumers rarely need full state. Trade-offs: small payloads and loose coupling vs. chatty callbacks and weak end-to-end traceability.
- **Event-Carried State Transfer** - Intent: put enough data in the event for consumers to maintain local read copies. When to use: consumers need producer data but you want to remove runtime dependency and improve availability. Trade-offs: better autonomy/availability vs. data duplication and replication lag.
- **Event Sourcing** - Intent: persist state as an append-only sequence of events; rebuild state by replay. When to use: audit, temporal queries, replay/debugging, very high write throughput. Trade-offs: powerful history and throughput vs. need for projections and event-schema versioning.
- **CQRS** - Intent: split read and write models so each scales and evolves independently. When to use: high read-to-write ratios, multiple distinct views, or different consistency needs for reads vs. writes. Trade-offs: independent scaling and clearer intent vs. eventual consistency between models and added moving parts (event bus, projections).
- **Saga (orchestrated and choreographed)** - Intent: maintain consistency across services without 2PC via chained local transactions plus compensations. When to use: a workflow spans services that own their own data and 2PC is undesirable. Trade-offs: scalability and no long-held locks vs. compensation complexity, idempotency requirements, and eventual consistency windows.
- **Process Manager / Saga Execution Coordinator** - Intent: a mediator that drives a multi-step flow and records progress in a replayable log. When to use: orchestrated sagas needing recoverability. Trade-offs: clear control and recovery vs. a coordination hotspot that must be highly available.
- **Dead Letter Channel** - Intent: divert messages that cannot be processed to a separate destination instead of losing them. When to use: any consumer that can fail, time out, or receive invalid/expired messages. Trade-offs: prevents data loss and enables triage/replay vs. operational burden of monitoring and draining the dead-letter store (and guarding against retry loops).
- **Idempotent Receiver** - Intent: make repeated processing of the same message safe. When to use: at-least-once delivery, retries, sagas, parallel/interleaved logs. Trade-offs: safe duplicates vs. tracking processed message IDs / designing operations to be naturally idempotent.

## Principles & best practices
- Confine ACID transactions to within a single component/aggregate; use BASE (events + eventual consistency) across components. (Garverick & McIver)
- Design for duplicate and out-of-order delivery from day one: idempotent handlers, correlation IDs, and resequencing where order matters. (Christudas)
- Prefer choreography for maximal decoupling and extensibility; reach for orchestration when a flow is complex, must be monitored, or needs central recovery.
- Keep events immutable and version them deliberately; with event sourcing the log is the system of record and must remain replayable over time.
- Make the orchestrator dumb and the log smart: store execution state in a distributed, replicated log so any coordinator instance can resume after a crash. (Christudas)
- Use dead-letter channels plus bounded retries with backoff; watch the dead-letter counter to avoid infinite reprocessing loops. (Morlion)
- Treat data loss as a design parameter: identify acceptable loss explicitly, then layer guaranteed delivery, persistence, and acknowledgements to approach (but rarely guarantee) zero loss. (Garverick & McIver)

## Anti-patterns & pitfalls
- **Assuming distributed ACID.** Writing code that flows from one cross-service step to the next as if external operations were atomic. Eventual consistency must be designed for, not discovered in production. (Garverick & McIver)
- **Retry-as-consistency.** Endlessly retrying a failing step waiting for eventually consistent data to converge, instead of modeling the flow as a saga. Common but cumbersome. (Garverick & McIver)
- **Non-idempotent consumers.** Duplicates from retries corrupt state; sagas in particular replay transactions on timeout.
- **Hidden choreography sprawl.** Pure broker topology with no end-to-end view makes it nearly impossible to reason about or debug overall behavior; invest in observability (message history, tracing) early.
- **Losing messages on failure.** No dead-letter strategy means rejected, expired, or overflowed messages vanish. (Morlion)
- **Orchestrator as single point of failure.** A mediator that holds in-memory state and cannot be recovered from a log undermines the resilience EDA is meant to provide.

## Decision guidance
- Choose EDA when you need high scalability/elasticity, asynchronous workflows, decoupled teams/services, or audit/replay - and you can accept eventual consistency and harder testing.
- **Notification vs. state transfer:** start with notification for low coupling; switch to state transfer when callback chatter or producer availability becomes the bottleneck.
- **Event sourcing:** adopt when audit, temporal queries, or replay are first-class requirements, or when write throughput must be very high; avoid if a simple CRUD store meets the need, since projections and versioning add real cost.
- **CQRS:** apply when read and write workloads differ sharply (high look-to-book) or when multiple read models are needed; do not split if a single model serves both well.
- **Choreography vs. orchestration:** default to choreography for simple, extensible flows; use orchestration (process manager / SEC) when the workflow is multi-step, must be monitored, or needs central recovery.
- **Saga vs. 2PC:** prefer sagas over two-phase commit for long-lived or cross-trust-boundary transactions to avoid prolonged resource locking; accept the obligation to write idempotent, commutable compensations.

## Source attribution
- EDA fundamentals, producer-consumer/pub-sub, event sourcing, CQRS rationale, eventual consistency/BASE, saga-for-consistency, idempotency, acceptable data loss: Garverick & McIver (2023), chs. 1, 10, 11.
- CQRS in depth (traditional vs. CQRS, command/event nomenclature, event-based CQRS meta-model, event store, event bus), saga explained, distributed saga (SEC, saga log, DAG, transaction characteristics): Christudas (2019), chs. 5, 15.
- Dead-lettering, retries with backoff/jitter, pub/sub vs. competing consumers, request/reply over a broker: Morlion (2024), chs. 3–5.

## Recent developments (2024–2026)

- **Data streaming matured into a core software category, with Kafka + Flink as the de facto backbone.** By 2026 the event-stream-processing market was tracked toward exceeding ~$14B, and Apache Kafka (transport/log) plus Apache Flink (stream processing) had become the default pairing for production EDA in fraud detection, personalization, supply chain, and AI feeding. Confluent publicly repositioned around Flink as the stream-processing standard. (Kai Waehner, "Top Trends for Data Streaming 2026," 2025; "The Data Streaming Landscape 2026," 2025)
- **AI/agents became a first-class driver of EDA.** A major 2025-2026 theme is wiring EDA to keep AI systems and agents fed with live, trustworthy data: Flink/Kafka pipelines continuously supply features and context to models, and "AI context engines" appear as a named streaming trend. EDA is increasingly framed as the connective tissue for agentic systems rather than just service decoupling. (Kai Waehner 2025-2026; programming-helper.com 2026)
- **Exactly-once at scale moved from aspiration to routine.** Kafka idempotent producers plus transactional guarantees, combined with Flink's checkpointing, made exactly-once semantics practical in finance, healthcare, and e-commerce where duplicates/loss are unacceptable. This shifts the "design for at-least-once and idempotency" advice: idempotency is still mandatory, but the platform now carries more of the guarantee. (Growin 2025; Kai Waehner 2026)
- **Streaming infrastructure trends: consolidation and "diskless" Kafka.** 2026 trend lists highlight platform consolidation, diskless/object-store-backed Kafka (decoupling storage from brokers for cheaper retention), real-time analytics, zero-loss guarantees, and cloud-sovereignty concerns. Some commentators argue raw event-streaming adoption is "topping out" as the category matures and attention shifts to higher-level processing and governance. (Kai Waehner 2026; Stanislav Kozlovski, "Event Streaming is Topping Out," 2025-2026)
- **The conceptual patterns (event notification, ECST, event sourcing, CQRS, sagas, choreography vs orchestration) are stable.** No fundamental reframing in 2024-2026; the movement is in tooling maturity, AI integration, and operational guarantees rather than new core patterns. The 2025 practitioner emphasis is on doing EDA "right" with quality, observability, and schema governance rather than adopting it for its own sake. (Growin, "Event Driven Architecture Done Right," 2025)
