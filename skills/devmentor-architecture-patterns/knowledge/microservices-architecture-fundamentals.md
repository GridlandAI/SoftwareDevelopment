# Microservices Architecture Fundamentals

**Domain:** 05 - Architecture Patterns and Styles
**Primary sources:** Building Microservices, 2nd Edition (Newman, 2021); Microservices Patterns, 2nd Edition (Richardson, 2024 MEAP); Microservices: Up and Running (Mitra & Nadareishvili, 2020); Production-Ready Microservices (Fowler, 2016); Embracing Microservices Design (Khan, Siddiqui & Oleson, 2021)

## Overview

A microservice architecture structures an application as a collection of two or more loosely coupled, independently deployable services, each modeled around a slice of the business domain. Newman defines microservices as "independently releasable services that are modeled around a business domain"; Richardson defines the architecture as "an architectural style that structures an application as a collection of two or more loosely design-time coupled, independently deployable services." Both definitions place independent deployability and loose coupling at the center, and both treat microservices as an opinionated form of service-oriented architecture (SOA) that fixes SOA's historical weaknesses: vague guidance on granularity, shared databases, and heavyweight vendor middleware.

The reason to adopt microservices is rarely technology for its own sake. Richardson frames the motivation as **fast flow**: the continuous delivery of small changes with rapid feedback, which large organizations cannot achieve when a single monolith forces every team to coordinate. Newman frames it as buying **options** at a cost. The central trade-off runs through every fundamentals decision: microservices give organizational scaling, technology heterogeneity, independent scaling, and robustness, but they import the full complexity of distributed systems (latency, partial failure, eventual consistency, operational overhead). Microservices are a choice to be justified, not a default.

## Core concepts

- **Independent deployability.** The ability to change one service, deploy it, and release it to users without deploying anything else. Newman calls this the single most important idea in the book and treats it as a forcing function: pursuing it forces loose coupling, stable contracts, and per-service pipelines, which yield many ancillary benefits. Richardson sharpens it: a service is independently deployable only if it (1) is a deployable component, (2) has its own deployment pipeline, and (3) is production-ready after being tested *in isolation* (no slow end-to-end tests).
- **Loose coupling.** A change to one service rarely requires a change to another. This is the precondition for team autonomy. Richardson defines four flavors (see Frameworks).
- **Modeled around a business domain.** Service boundaries follow business capabilities / subdomains rather than technical layers. This prioritizes high cohesion of *business* functionality over high cohesion of *technical* functionality, so that a typical business change lands inside a single service rather than rippling across presentation/logic/data tiers (the classic three-tier problem).
- **Owning their own state (database-per-service).** A service encapsulates its own data; other services ask for data via the API rather than reaching into a shared database. This is the analog of encapsulation in OO and is, per Newman, the practice people struggle with most. Shared databases are "one of the worst things you can do" for independent deployability.
- **Information hiding (Parnas).** Hide as much as possible inside the service boundary; expose as little as possible. "The connections between modules are the assumptions which the modules make about each other" - minimizing those assumptions is what keeps services changeable. Richardson's **Iceberg principle** restates this: a service's API (surface) should be tiny relative to its implementation (mass below the waterline); elements should be "deep, not wide."
- **Size is the least interesting property.** Newman explicitly de-emphasizes size ("a microservice should be as big as your head"). The useful proxies are: as small an *interface* as possible (Richardson), and the boundary that minimizes cross-team coordination (Mitra & Nadareishvili). "Microservice architecture != lots of little services" - Richardson is suspicious of any architecture with more services than teams.
- **Fast flow + the success triangle.** Richardson: fast flow requires three reinforcing elements - DevOps (the process), Team Topologies (the organization, with autonomous stream-aligned teams), and an architecture that enables both. Microservices are the architecture leg; without the other two, the architecture cannot deliver.
- **Distributed vs local operations.** A local operation is handled by one service; a distributed operation spans services. Distributed operations are the source of most microservices difficulty and constrain how you may draw boundaries.

## Frameworks & models

### Richardson's four flavors of coupling
The most precise lens for boundary and design decisions. Coupling is "the ability of one element to influence another."

1. **Design-time coupling** - likelihood that a change to one element forces a change to another. The flavor that matters most for fast flow; loose design-time coupling enables team autonomy and lets organizations scale (deployments-per-developer can stay flat or rise as headcount grows, per *Accelerate*).
2. **Build-time coupling** - changing one element forces another to be recompiled/retested. Affects feedback latency. Usually a non-issue *between* services (each builds in isolation) but matters *within* a service and within a monolith. Mitigate by splitting API projects from implementation projects so clients depend only on the stable API.
3. **Runtime coupling** - one element's failure causes another's failure. Reduces availability (availability of a chained call is the *product* of the participants' availabilities) and raises latency (response time is the *sum*). The "Christmas tree light" / "death star" anti-pattern is a long chain of runtime-coupled services. Mitigate by merging subdomains into one service for critical operations, moving responsibilities to reduce hops, or using asynchronous messaging.
4. **Infrastructure coupling** - two elements share infrastructure (DB server, cluster) and contend for resources or are affected by each other's config changes. Mitigate by isolating truly critical services on their own infrastructure.

### Newman's coupling taxonomy (loose → tight)
Domain coupling (acceptable, unavoidable) → Pass-through coupling (passing data only because a downstream needs it; problematic) → Common coupling (shared mutable data, e.g. a shared DB; often signals weak cohesion) → Content coupling (reaching into another service's internal data/DB directly; "pathological," always avoid). Plus **temporal coupling**: synchronous callers require the callee to be up simultaneously - break it with asynchronous messaging where possible.

### Domain-Driven Design as the boundary tool
- **Ubiquitous Language** - shared vocabulary between domain experts and developers, reflected in code and in service interfaces. Boundaries appear where a term changes meaning (e.g., "account" differs across identity, customer, and accounting contexts - Mitra & Nadareishvili).
- **Aggregate** - a self-contained domain concept with state, identity, and a lifecycle (often a state machine). One aggregate is owned by exactly one service; a service may own several aggregates. An aggregate can *reject* invalid state transitions.
- **Bounded context** - a larger organizational boundary containing one or more aggregates with an explicit external interface. Start by making services span entire bounded contexts (fewer, coarser services); subdivide along aggregate boundaries later. Contexts nest ("turtles all the way down"), and you can hide internal decomposition behind a coarse-grained API.
- **Context mapping** (Mitra & Nadareishvili) - describes how bounded contexts collaborate: *shared kernel* (overlapping model + code; high coordination, avoid in microservices), *customer–supplier*, *conformist*, *anti-corruption layer* (translation layer protecting a downstream from an upstream's model), *open host service* (published standard interface for many consumers), *separate ways*.

### Decomposition strategies
- **By business capability / subdomain (DDD)** - the primary, most stable mechanism (Newman, Richardson, Mitra & Nadareishvili). Boundaries track the business and tend to stay stable as the domain evolves.
- **Event Storming** (Brandolini) - a fast, collaborative workshop to discover the domain model. Capture domain events (orange stickies, past tense) → commands (blue) → aggregates (yellow) → cluster into bounded contexts. Mitra & Nadareishvili position it as a lightweight, hours-not-weeks alternative to full DDD for *finding* boundaries, optionally closing with a difficulty × competitive-edge prioritization matrix.
- **SEED(S)** (Mitra & Nadareishvili) - Seven Essential Evolutions of Design for Services: identify actors → identify their Jobs To Be Done → sequence diagrams → derive actions and queries → specify each as OpenAPI/GraphQL → get feedback → implement. Treats APIs/services as products designed customer-first.
- **Alternative drivers (Newman)** - domain is the default, but you may also decompose by *volatility* (extract fast-changing parts; good when time-to-market is the goal), *data/security* (isolate PII or PCI-scope data into a separate zone), *technology* (a part needs a different runtime), or *organization* (align to teams; never split a service across teams, and never make a horizontal technical slice - the "onion architecture" mistake).

### Service sizing - the Universal Sizing Formula (Mitra & Nadareishvili)
1. Start with just a few services, roughly aligned to bounded contexts.
2. Keep splitting as the application and teams grow, guided by the need to *avoid coordination*.
3. Optimize for a decreasing-coordination trajectory, not for getting sizing "perfect" now. Splitting a service is easier than merging; start coarse.

## Patterns

- **Modular monolith** - Intent: a single deployable unit partitioned into well-bounded modules (optionally with a decomposed database). When to use: the recommended default for new systems and a strong alternative to microservices (Shopify); a stepping-stone that lets you iterate on boundaries cheaply. Trade-offs: keeps deployment simple and feedback fast; the database often resists decomposition, making a later split harder; can still outgrow its single pipeline and lose team autonomy at large scale. *(Newman, Richardson)*
- **Strangler Fig (migration)** - Intent: incrementally migrate functionality from a monolith into services, delivering value and getting design feedback throughout. When to use: any monolith-to-microservices migration. Trade-offs: avoids the "big bang rewrite" (which "often results in a big bang"); requires discipline to break apart a legacy codebase. *(Richardson; detailed in Newman's companion work)*
- **Database-per-service** - Intent: each service owns and hides its data. When to use: as the default for any microservice. Trade-offs: enables independent deployability and per-service data-store choice; complicates cross-service queries, reporting, and consistency (drives API composition, CQRS, sagas). Services may *share a physical cluster* as long as no two own the same logical table space (Mitra & Nadareishvili). *(All sources)*
- **Aggregator / API composition (intro)** - Intent: a service or gateway calls multiple services and combines results to answer a query or coordinate a task. When to use: distributed queries and orchestrated workflows. Trade-offs: simple, but couples the aggregator to many services and can centralize logic. *(Khan et al.; deep coverage in the catalog file)*

(Full pattern catalog with sagas, CQRS, transactional outbox, API gateway/BFF, circuit breaker, etc. is in `microservices-patterns-catalog.md`.)

## Principles & best practices

- **Monolith-first.** Richardson and Newman both recommend starting with a (modular) monolith. You rarely understand a new domain well enough to draw stable boundaries, and APIs are bets on what is stable; betting wrong in a distributed system is expensive. Migrate only after confirming the monolith is the actual root cause of slow delivery (it may instead be a missing pipeline, missing DevOps, or missing Team Topologies).
- **Independent deployability is the discipline, not just the capability.** Deploy one service at a time as the default release approach.
- **Don't share databases.** Hide state; expose behavior. Treat requests to a service as something the owning service may *reject* if invalid (avoid thin CRUD wrappers that leak state-machine logic).
- **Communicate in business terms.** Service interfaces should speak the ubiquitous language, like forms passed around an organization.
- **Choose communication style before technology** (Newman): decide synchronous-blocking vs asynchronous-non-blocking, and request-response vs event-driven, *then* pick the implementing tech. Don't use Kafka for request-response, etc.
- **Conway's Law and its inverse.** Architecture mirrors org communication structure (Conway). Adopting microservices reshapes the org into many small, autonomous teams (the inverse, per Fowler) - plan for the siloing and cross-team communication cost this creates.
- **Apply DRY judiciously across services.** Shared libraries of *unstable business rules* reduce team autonomy by forcing lockstep upgrades; sometimes controlled duplication is the better trade (Richardson).
- **Introduce supporting technology incrementally** (Newman): log aggregation and correlation IDs are near-prerequisites; containers/Kubernetes, streaming, and serverless should arrive when the pain justifies them, not on day one.

## Anti-patterns & pitfalls

- **Distributed monolith** - services that must be deployed together / change in lockstep. Combines the worst of both worlds: distributed-systems pain with no independent deployability. Usually caused by tight design-time coupling and shared data. *(Newman, Richardson)*
- **Premature / over-decomposition ("More the Merrier")** - too many tiny services too early, before the domain is understood. Causes crushing complexity, network overhead, and brittle runtime coupling; SnapCI famously merged services back into a monolith before re-splitting later. Be suspicious of more services than teams. *(Newman, Richardson, Mitra & Nadareishvili)*
- **Boundaries on technical seams** - horizontal/three-tier splits, "onion architecture," or declaring every serverless function a microservice. Business changes then cut across services. *(Newman, Mitra & Nadareishvili)*
- **Shared database / content coupling** - multiple services reading and writing the same tables, or one service reaching into another's database. *(Newman, Mitra & Nadareishvili, Khan et al.)*
- **Microservices for startups / brand-new products** - unstable domains mean unstable boundaries, and small teams pay the "microservice tax." Wait for the domain and team to stabilize. *(Newman)*
- **Microservices for customer-installed software** - pushes operational complexity onto customers who can't absorb it. *(Newman)*
- **Distributed transactions / 2PC for coordination** - injects latency and distributed locking; "just say no" and use sagas instead. *(Newman, Mitra & Nadareishvili)*
- **Adopting the architecture without the rest of the success triangle** - microservices without DevOps and Team Topologies is "buying a Ferrari and pulling it with a horse." *(Richardson)*

## Decision guidance

1. **Default to a monolith** (ideally modular) for new and small systems. Reach for microservices only when an application *and its organization* have grown to the point that team autonomy is declining and the single deployment pipeline is a bottleneck - and only after ruling out cheaper fixes (modularize, add a pipeline, adopt DevOps/Team Topologies).
2. **Where microservices fit well:** large organizations needing many teams to work without contention; SaaS needing 24/7 independent releases and elastic scaling; systems needing genuine technology heterogeneity; data/regulatory isolation needs. **Where they don't:** startups, unstable domains, tiny teams, customer-installed software.
3. **Find boundaries with the domain first** (DDD bounded contexts / Event Storming / SEED(S)), then let other drivers (volatility, data/security, technology, org) refine. Start coarse and split toward decreasing coordination.
4. **Engineer for loose coupling on all four flavors:** stable, small APIs (design-time); per-service pipelines and API/impl split (build-time); minimize synchronous chains, prefer async for critical paths (runtime); isolate critical services' infrastructure (infrastructure).
5. **Record the trade-offs.** Every boundary and style choice is a fixed bargain; capture the reasoning (e.g., in an ADR) so the "least-worst" choice is defensible later.

## Source attribution

- Definition, independent deployability, information hiding, owning state, size, advantages/pain points, when (not) to use, monolith taxonomy, Conway's Law: Newman 2021, ch. 1.
- Coupling/cohesion, Newman's coupling taxonomy, alternatives to domain decomposition, DDD (ubiquitous language, aggregate, bounded context, nesting), Event Storming process: Newman 2021, ch. 2.
- Communication styles (sync/async, request-response/event-driven), temporal coupling: Newman 2021, ch. 3.
- Distributed transactions, 2PC, sagas: Newman 2021, ch. 5.
- Fast flow, success triangle (DevOps + Team Topologies + architecture), MSA definition, monolith-first, Strangler Fig, distributed-monolith risk, four flavors of coupling, Iceberg/DRY/dependency principles, essential characteristics, benefits: Richardson 2024 MEAP, ch. 1–7.
- SEED(S), Universal Sizing Formula, context mapping, Event Storming as lightweight DDD, data delegate / data lake / saga routing slip / event sourcing / CQRS intros: Mitra & Nadareishvili 2020, ch. 3–5.
- Organizational challenges, Inverse Conway's Law, four trade-offs, production-readiness framing: Fowler 2016, ch. 1–2.
- Communication/timeout/retry-storm pitfalls, design principles list: Khan, Siddiqui & Oleson 2021, ch. 1, 3, 6.

## Recent developments (2024–2026)

- **The "monolith resurgence" became a mainstream consensus, not a contrarian take.** By 2025-2026 the industry narrative had shifted decisively from "microservices by default" to "modular monolith first, microservices only at scale." Widely cited (though vendor-blog-sourced) figures claim roughly 42% of organizations that adopted microservices consolidated at least some services back into larger deployable units, citing debugging complexity, operational overhead, and network latency. A common 2025 sizing heuristic: 1-10 developers start with a monolith, 10-50 favor a modular monolith, and microservices pay off at ~50+ developers / many autonomous teams. Treat the precise percentages with caution, but the directional shift toward modular monoliths is genuine and broad. (Java Code Geeks 2025; byteiota 2026; SoftwareSeni 2025)
- **Amazon Prime Video's case study crystallized the cost argument.** Prime Video's Video Quality Analysis team publicly moved from a distributed microservices/serverless design to a single-process monolith and reported ~90% infrastructure cost reduction plus better scaling. This widely referenced example is now the standard citation when arguing that microservices can be the wrong default for tightly coupled, high-throughput workloads. (Augusto Digital 2024-2025; foojay.io 2025)
- **Cost quantification entered the decision framework.** Recent practitioner writing puts microservices infrastructure at roughly 3.75x-6x the cost of an equivalent monolith and adds 1-2 platform engineers (~$140k-360k/year) to the bill. This reinforces Richardson's and Newman's "monolith-first, justify the tax" guidance with concrete numbers. (Java Code Geeks 2025; Medium/Piwosz 2025)
- **Team Topologies and "fast flow" stayed the dominant framing.** The 2024-2026 discourse consistently ties the microservices-vs-monolith decision to organizational scale (number of stream-aligned teams) rather than technical elegance, validating Richardson's success-triangle (DevOps + Team Topologies + architecture). The practical takeaway hardened: do not adopt microservices to fix a problem that better modularization, a deployment pipeline, or DevOps would solve more cheaply. (foojay.io 2025; Java Code Geeks 2025)
- **AI-assisted development is reshaping the boundary economics.** A new argument in 2025-2026 is that LLM coding assistants lower the cost of maintaining a large modular monolith (easier navigation, refactoring, and boundary enforcement), further weakening the "split for comprehensibility" rationale while leaving the "split for independent deployment and team autonomy" rationale intact. (Java Code Geeks 2026; SoftwareSeni 2025)
