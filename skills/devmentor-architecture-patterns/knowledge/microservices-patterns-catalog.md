# Microservices Patterns Catalog

**Domain:** 05 - Architecture Patterns and Styles
**Primary sources:** Microservices Patterns, 2nd Edition (Richardson, 2024 MEAP); Building Microservices, 2nd Edition (Newman, 2021); Microservices: Up and Running (Mitra & Nadareishvili, 2020); Embracing Microservices Design (Khan, Siddiqui & Oleson, 2021)

## Overview

This file is the centerpiece reference for the microservice architecture pattern language, organized after Chris Richardson's structure and enriched with Newman's communication and workflow patterns, Mitra & Nadareishvili's data patterns, and Khan et al.'s communication and cross-cutting patterns. A **pattern** is a reusable solution to a problem in a context (Alexander; Gamma et al.); a **pattern language** is a set of related patterns that together solve problems in a domain. Richardson stresses that a well-formed pattern states its **forces** (the competing concerns), its **resulting context** (benefits, drawbacks, and *new* problems introduced), and its **related patterns** (predecessor, successor, alternative, generalization, specialization). The Microservice Architecture pattern is the predecessor of nearly every pattern below: choosing it creates the problems the rest solve.

Richardson layers the language into **infrastructure patterns** (outside development), **application-infrastructure patterns** (infrastructure that impacts development), and **application patterns** (faced by developers building services), grouped by problem area: service collaboration, communication, security, testing, deployment, observability, and cross-cutting concerns.

## Core concepts

- **Local vs distributed operation.** A distributed operation spans services and must be implemented with **service collaboration patterns**. There are command-side patterns (Saga) and query-side patterns (API Composition, CQRS, Command-side replica).
- **Eventual consistency.** Services are transaction boundaries; you cannot wrap a multi-service operation in one ACID transaction. You trade ACID atomicity for a coordinated sequence of local transactions plus compensation.
- **Communication style precedes technology.** Decide synchronous-blocking vs asynchronous-non-blocking and request-response vs event-driven before choosing REST/gRPC/Kafka/etc. (Newman).
- **An API is a bet** on what is stable; patterns mostly exist to keep change localized when the bet holds.

## Patterns

### Decomposition patterns

- **Decompose by business capability / subdomain** - Intent: define services around bounded contexts and subdomains so business changes stay inside one service. When to use: as the primary decomposition mechanism. Trade-offs: stable boundaries aligned to teams; requires real domain understanding. *(Richardson; Newman; Mitra & Nadareishvili)*
- **Decompose by volatility / data / technology / organization** - Intent: secondary drivers that refine domain boundaries. When to use: when time-to-market, PII/PCI isolation, runtime needs, or team alignment dominate. Trade-offs: each can pull boundaries away from the domain; mix deliberately. *(Newman)*
- **Strangler Fig** - Intent: incrementally migrate a monolith to services, routing traffic to new services while the monolith shrinks. When to use: every monolith→microservices migration. Trade-offs: delivers value and feedback continuously vs. the high risk of a big-bang rewrite; harder to engineer against a tangled legacy codebase. *(Richardson; Newman)*

### Database & shared-data patterns

- **Database per service** - Intent: each service owns and hides its own data behind its API. When to use: default. Trade-offs: enables independent deployability and polyglot persistence; complicates cross-service queries, joins, reporting, and consistency. Physical DB clusters may be shared as long as no two services own the same logical tables (Mitra & Nadareishvili). *(All sources)*
- **Data delegate (shared-data hiding)** - Intent: when several services need the same data, make one service the authoritative owner and have others call it instead of touching the table. When to use: read/write access to data currently shared via a table. Trade-offs: restores ownership and loose coupling; adds a network hop and runtime coupling. *(Mitra & Nadareishvili)*
- **Data lake / read-only replication** - Intent: stream copies of data from system-of-record services into a query-optimized read-only sink for analytics, ML, audit, and reporting. When to use: cross-service read-only needs. Trade-offs: solves reporting without sharing operational data; the lake must never be a database of record (no operational writes). *(Mitra & Nadareishvili; Newman discusses reporting via streaming)*

### Service collaboration - command side

- **Saga** - Intent: maintain consistency across services for a distributed command by chaining local ACID transactions, each with a **compensating transaction** for rollback. When to use: a write operation (workflow) spans services that own their own data; whenever you would otherwise reach for a distributed transaction. Trade-offs: avoids distributed locking and the availability collapse of 2PC, and makes business processes explicit; gives no global atomicity or isolation - you get *semantic* (not literal) rollback, must reason about intermediate states, and must handle concurrency yourself.
  - **Choreography** - services react to each other's events; no central coordinator. Lowest coupling; harder to see overall flow and saga state (use a **correlation ID** and an event-consuming view). Newman prefers this when multiple teams own the steps.
  - **Orchestration** - a central orchestrator (e.g., an Order Processor) issues commands and handles failures. Visible, easy-to-reason flow; more domain coupling and a risk of anemic services as logic centralizes. Newman prefers this when one team owns the whole saga.
  - **Implementation notes:** reorder steps so hard-to-compensate actions come last; mix backward recovery (compensation) and forward recovery (retry) per step; sagas can nest; the saga *sequence is meaningful* (Mitra & Nadareishvili's routing-slip model passes compensating-action pointers along the steps). *(Newman ch. 5; Richardson; Mitra & Nadareishvili ch. 5)*
- **Transactional outbox** - Intent: atomically update the database and "send" a message by writing the message to an outbox table in the same local transaction; a relay/CDC process later publishes it. When to use: any service that must update state and reliably publish an event/command (the core of reliable saga messaging). Trade-offs: guarantees no lost/ghost messages without distributed transactions; needs a message relay (polling or change-data-capture, e.g., Debezium) and at-least-once/idempotent consumers. *(Richardson - transactional messaging; supported by Newman's streaming/Debezium notes)*

### Service collaboration - query side

- **API composition** - Intent: implement a distributed query by querying each owning service and joining results in memory (in a composer service or the API gateway). When to use: simple cross-service reads. Trade-offs: easy; can be inefficient (multiple round trips), couples the composer to many services, and in-memory joins scale poorly for large datasets. *(Richardson; akin to Khan et al.'s Aggregator)*
- **CQRS (Command Query Responsibility Segregation)** - Intent: separate the write model from one or more read models; maintain a read-only replica that combines data from multiple services, kept up to date via events. When to use: queries that span services or need a different shape/store than the write side; high read scalability. Trade-offs: efficient, scalable, independent read scaling; adds complexity, replication lag, and eventual consistency between command and query sides. *(Richardson; Mitra & Nadareishvili ch. 5)*
- **Command-side replica** - Intent: maintain a local read-only replica of another service's data so a command can query it efficiently without a synchronous call. When to use: a command needs another service's data on a hot path. Trade-offs: removes runtime coupling on the write path; adds replication and staleness. *(Richardson)*
- **Event sourcing** - Intent: persist state as an append-only sequence of domain events; current state is derived (a "first-level derivative") by replaying events. When to use: pairs with CQRS; needs full audit/temporal history; helps avoid relational data sharing. Trade-offs: reliable event publishing and rich history; unfamiliar model, event-schema evolution challenges, queries require projections. Anti-pattern: using database *entities* as events (Khan et al.). *(Mitra & Nadareishvili ch. 5; Richardson)*

### Communication style & reliability

- **Synchronous blocking (request-response)** - Intent: caller blocks awaiting a response (REST, gRPC). When to use: simple architectures, short chains, when the result is needed immediately. Trade-offs: familiar; creates temporal + runtime coupling and resource contention; long chains multiply failure surface and latency. *(Newman ch. 3; Khan et al.)*
- **Asynchronous messaging (non-blocking)** - Intent: caller emits a message/event and continues; the broker decouples sender and receiver in time. When to use: long-running work, long chains you can't restructure, breaking runtime coupling on critical paths. Trade-offs: higher availability and decoupling; more moving parts, harder debugging, partial/eventual outcomes that clients must poll or subscribe for. *(Newman ch. 3; Mitra & Nadareishvili; Khan et al.)*
- **Event-driven / publish-subscribe** - Intent: services broadcast facts (events) to a topic; interested services subscribe without the publisher knowing them. When to use: choreographed sagas, fan-out, loosely coupled notifications (Observer-style dependency inversion). Trade-offs: minimal domain coupling and easy multi-consumer fan-out; flow is implicit and harder to trace. *(Newman; Richardson - dependency inversion via events)*
- **Communication through common data** - Intent: services collaborate by reading/writing a shared data store. When to use: rarely; mostly legacy or bulk hand-off. Trade-offs: simple but reintroduces common/content coupling. *(Newman ch. 3)*
- **Circuit breaker** - Intent: stop calling a failing dependency after a threshold, fail fast, and probe for recovery. When to use: synchronous calls to dependencies that can become slow/unavailable. Trade-offs: prevents resource exhaustion and cascading failure; needs tuning of thresholds and fallbacks. *(Khan et al.; Richardson - reliability)*
- **Retry with backoff and jitter** - Intent: retry only *transient* failures with increasing wait + jitter and a bounded counter. When to use: idempotent transient failures. Trade-offs: improves success odds; misused on long synchronous chains it causes a **retry storm** (each layer's timeout triggers retries that flood and topple the system). Pair with circuit breakers and timeouts; avoid retrying long sync chains. *(Khan et al. ch. 6)*
- **Timeouts** - Intent: bound how long a caller waits so it can fail and free resources. When to use: every synchronous call. Trade-offs: necessary for responsiveness; a poorly chosen large timeout is itself an anti-pattern (callers wait too long), so combine with circuit breakers and async alternatives. *(Khan et al. ch. 6)*

### External API patterns

- **API gateway** - Intent: a single entry point that routes external traffic to services and handles cross-cutting edge concerns (auth, TLS termination, rate limiting, aggregation). When to use: most microservice systems with external clients. Trade-offs: simplifies clients and centralizes edge concerns; can become a bottleneck or a place where logic over-centralizes. *(Richardson; Newman; Khan et al.)*
- **Backends for Frontends (BFF)** - Intent: a dedicated gateway per client type (web, mobile, partner) tailored to that client's needs. When to use: divergent client requirements. Trade-offs: optimizes each client experience and avoids one bloated gateway; more gateways to own. *(Richardson; Newman)*

### Service discovery

- **Client-side discovery** - Intent: the client queries a service registry for instances and load-balances itself. Trade-offs: fewer hops; couples clients to the registry. *(Khan et al.)*
- **Server-side discovery** - Intent: the client calls a load balancer/router that consults the registry. Trade-offs: clients stay simple; the router is another component to run. *(Khan et al.; Richardson)*
- **Service registry / self-registration / 3rd-party registration** - Intent: track live instances and their addresses. Trade-offs: enables dynamic scaling; the registry must itself be highly available. *(Richardson)*

### Security

- **Access token (e.g., JWT)** - Intent: the API gateway authenticates the user and passes a signed token carrying identity/roles to each service, which validates it and extracts the principal. When to use: propagating user context across services. Trade-offs: stateless and scalable; needs key management and token-lifetime/revocation handling. *(Richardson - security patterns)*

### Cross-cutting concerns

- **Microservice chassis** - Intent: a framework that implements cross-cutting concerns (config, discovery, observability, security wiring). Trade-offs: removes boilerplate and enforces consistency; couples services to the chassis version. *(Richardson; Khan et al.)*
- **Service template** - Intent: a copyable source template built on the chassis to bootstrap a new service. Trade-offs: fast, consistent service creation; templates must be maintained. *(Richardson)*
- **Externalized configuration** - Intent: supply config/secrets at runtime rather than baking them in. Trade-offs: portable across environments (12-factor); needs a secrets/config store. *(Richardson; Khan et al.)*
- **Service mesh** - Intent: an infrastructure layer (sidecars) that handles traffic management, mTLS, policy, identity, and observability, decoupling these from service code. When to use: many services needing consistent reliability/security/observability. Trade-offs: consistent cross-cutting behavior without per-service code; operational complexity and latency overhead. Building protocol translation *inside* the service is the anti-pattern this replaces. *(Khan et al. ch. 6, 7)*

### Deployment patterns

- **Service as container / VM** - Intent: package each service as a container image (or VM) with one service per host. Trade-offs: isolation and portability; orchestration overhead (Kubernetes). Container deployment is a *specialization* of single-service-per-host. *(Richardson; Newman)*
- **Serverless / FaaS deployment** - Intent: upload code; the platform runs/scales it. Trade-offs: minimal ops and on-demand scaling for suitable workloads; not a fit for all latency/runtime profiles. *(Richardson; Newman)*
- **Service deployment platform + GitOps** - Intent: an automated, self-service platform where the desired system state lives in Git and automation reconciles production to it. Trade-offs: auditable, repeatable, fast deploys; investment in platform tooling. *(Richardson)*
- **Deployment strategies (rolling / canary / blue-green)** - Intent: release changes gradually to limit blast radius. When to use: production rollouts. Trade-offs: safer releases and easy rollback; more pipeline machinery. Rolling is the Kubernetes default; canary uses a 2–5% pool. *(Khan et al. ch. 8; Fowler - stability)*

### Observability patterns

- **Health check API** - endpoint reporting service health for the platform/registry.
- **Log aggregation + correlation IDs** - centralize logs; tag a request's whole call chain with one ID so a distributed flow can be reconstructed. Newman treats log aggregation + correlation IDs as near-prerequisites.
- **Distributed tracing** - assign a request a trace ID and follow it across services to find latency/bottlenecks (Jaeger, Honeycomb, LightStep).
- **Application metrics** - counters/gauges exposed to a metrics server with normal/warning/critical thresholds.
- **Exception tracking** - dedupe and route exceptions to a tracking service.
- **Audit logging** - record user actions for security/compliance.
*(Richardson - observability group; Newman ch. 1; Fowler ch. 6)*

### Testing patterns

- **Consumer-driven contract test** - Intent: verify a service meets the expectations of each client without testing them together. When to use: to make services independently deployable. Trade-offs: replaces slow/brittle end-to-end tests; contracts cost maintenance (excessive fine-grained, tightly coupled services make contracts churn). *(Richardson ch. 15–16; Newman; Khan et al. ch. 9)*
- **Consumer-side contract test** - verify the client can correctly talk to the service's API.
- **Service component test** - test a service in isolation with collaborators replaced by test doubles.
- **Minimize end-to-end tests** - move them out of the deployment pipeline; favor isolated, fast, reliable service-level tests; test critical user journeys. *(Richardson; Khan et al.)*

## Principles & best practices

- Choose communication style before technology; prefer async to break temporal/runtime coupling on critical paths.
- Use sagas - never distributed transactions/2PC - for cross-service writes; make rollback semantic and order steps to minimize compensation.
- Pair the transactional outbox with idempotent consumers for reliable messaging.
- Separate read and write concerns (API composition for simple reads, CQRS for complex/spanning reads).
- Protect synchronous calls with timeouts + circuit breakers + bounded retries (with jitter); never retry long sync chains.
- Make services independently testable with contract tests; keep end-to-end tests out of the pipeline.
- Push cross-cutting concerns into a chassis/template or service mesh rather than reimplementing per service.

## Anti-patterns & pitfalls

- **Distributed transactions / 2PC** for coordination (use sagas).
- **Retry storm** from retries on long synchronous chains; **timeout anti-pattern** from oversized timeouts.
- **Christmas-tree-light / death-star** long synchronous chains (tight runtime coupling).
- **Aggregator/composer over-coupling** and in-memory joins over large datasets.
- **Entities-as-events** in event sourcing; treating a data lake as a system of record.
- **Protocol translation inside the service** instead of at a gateway/mesh.
- **Excessive contract maintenance** as a symptom of too many tightly coupled services.

## Decision guidance

- **Cross-service write?** Saga (choreography if multiple teams own steps; orchestration if one team does). Make messaging reliable with the transactional outbox.
- **Cross-service read?** API composition for simple cases; CQRS/command-side replica when queries are complex, span many services, or need scale/different shape.
- **Synchronous dependency on a hot path?** Add timeout + circuit breaker + bounded retry; consider async messaging or a command-side replica to remove runtime coupling.
- **External clients?** API gateway; split into BFFs when client needs diverge.
- **Many services, repeated cross-cutting needs?** Chassis/template first; service mesh when consistency at scale justifies the overhead.
- **Independent deployability?** Contract tests in, end-to-end tests out of the pipeline.

## Source attribution

- Pattern-language structure, forces/resulting-context/related-patterns, service collaboration (Saga, API composition, CQRS, command-side replica), communication groups, transactional messaging, access token, observability, testing, deployment/GitOps, chassis/template: Richardson 2024 MEAP, ch. 7 (pattern-language overview) and referenced chapters.
- Saga choreography vs orchestration, compensating/semantic rollback, step reordering, forward/backward recovery, correlation IDs, sync/async/event-driven/common-data communication styles, BFF, log aggregation, 2PC critique: Newman 2021, ch. 3, 5.
- Data delegate, data lake, saga routing slip, event sourcing, CQRS, sharing physical DB clusters: Mitra & Nadareishvili 2020, ch. 5; SEED(S)/Event Storming ch. 3–4.
- Circuit breaker, retry/retry storm, timeout anti-pattern, API gateway, service discovery (client/server-side), service mesh, deployment strategies, contract testing, entities-as-events: Khan, Siddiqui & Oleson 2021, ch. 6–9.

## Recent developments (2024–2026)

- **The pattern language is stable; the 2024-2026 movement is about when to apply it, not new patterns.** Richardson's and Newman's catalogs (saga, CQRS, API composition, transactional outbox, gateway/BFF, circuit breaker, chassis, contract tests) remained the reference vocabulary, with no fundamental additions. The notable shift is the "modular-monolith-first" consensus that recommends deferring most of these distributed patterns until team scale justifies them. (Java Code Geeks 2025; SoftwareSeni 2025)
- **The service-mesh "push cross-cutting concerns to the mesh" recommendation got cheaper via ambient mode.** Istio ambient mode (ztunnel + waypoints, no sidecars) reached production maturity, lowering the overhead that previously made the chassis-vs-mesh trade-off lean toward in-process libraries for small estates. This strengthens the catalog's "service mesh when consistency at scale justifies the overhead" guidance. (Cloud Native Now 2026; ambientmesh.io)
- **The chassis pattern has concrete, mainstream implementations now.** .NET Aspire's `ServiceDefaults` (OTel + health checks + discovery + Polly resilience in one shared library) and Dapr's portable sidecar building blocks (service invocation, pub/sub, state, secrets) are widely adopted realizations of the microservice-chassis pattern the catalog describes. (The New Stack; Microsoft Learn Aspire/Dapr docs 2024-2026)
- **Saga/transactional-outbox reliability is increasingly platform-backed.** Kafka transactions plus exactly-once Flink processing, and broker-level guarantees, reduce how much idempotency and dedup logic teams hand-write, though the catalog's core advice (idempotent consumers, semantic compensation, outbox) still holds. (Kai Waehner 2026; Growin 2025)
- **AI agents add a new consumer for these patterns.** Model Context Protocol (MCP) interfaces are emerging as an agent-facing contract layer over service estates, sitting alongside the API gateway/BFF patterns; observability patterns (W3C Trace Context, OpenTelemetry/OTLP) are now the default tracing realization rather than one option. (n1n.ai 2026; istio.io)
