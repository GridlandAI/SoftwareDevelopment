# API Design and REST Patterns

**Domain:** 05 - Architecture Patterns and Styles
**Primary sources:** RESTful Web API Patterns and Practices Cookbook (Amundsen, 2022); RESTful API Design Patterns and Best Practices (Jarzyna & Amzani, 2025); Pro RESTful APIs with Micronaut (Patni, 2023)

## Overview
APIs have shifted from being a low-level integration mechanism to being a product in their own right: an interface to the business that lets internal teams, partners, and increasingly AI agents build new solutions. Designing a good API is therefore a design discipline ("APX," the API equivalent of UX) as much as an engineering one. The dominant style is REST, an architectural style abstracted by Roy Fielding from the architecture of the World Wide Web itself. REST is not a standard or a set of hard rules but a named collection of constraints that, applied together, induce desirable system properties: scalability, evolvability, simplicity, visibility, portability, and reliability over the scale of decades.

This topic covers how to model resources, apply the REST constraints, climb the maturity models, use hypermedia, design URIs and status codes, version and evolve APIs without breaking consumers, paginate and filter collections, standardize errors, achieve idempotency, secure the API, and choose between REST and alternative styles (RPC/gRPC, GraphQL, event-driven). The throughline is Amundsen's umbrella principle: "Leverage global reach to solve problems you haven't thought of for people you have never met." Good API design assumes strangers, change, and a long lifetime.

## Core concepts

### REST and its intellectual roots
REST (Representational State Transfer) is one of six architectural styles Fielding identified in his 2000 dissertation; he chose constraints precisely to induce the properties the web needed. Amundsen frames REST as the meeting point of three ideas: Fielding's REST (architecting to scale and modify safely over time), Berners-Lee's web (a universal linked information system favoring generality and portability, and the "Rule of Least Power"), and Alan Kay's extreme late binding (building systems you can change while they are still running). The internet is always running, so any service we deploy changes a live system.

### Resources and representations
A resource is the conceptual target of a hypertext reference (a customer, an order). It is identified by a URI and may have multiple representations (JSON, XML, HTML). Clients and servers negotiate the representation through HTTP content negotiation (Accept header; a 406 is returned if the server cannot satisfy it). Crucially, a representation is never the raw internal data store; it is an external message shaped for the consumer's use case.

### Hypermedia
Hypermedia is the use of links and forms embedded in responses so that clients can discover and activate the next valid actions at runtime rather than hardcoding them. Links express navigation; forms express actions that take input. This is rooted in Gibson's and Norman's notion of "affordances" - the things in a response that afford further action. Hypermedia is what lets a single generic client (like an HTML browser) be repurposed for unlimited applications without code changes, and it is the mechanism that decouples clients from servers so servers can evolve.

### Vocabularies and the semantic gap
Even generic message formats need to carry meaning. Information architecture supplies three elements: ontology (particular meaning of terms), taxonomy (arrangement of parts), and choreography (rules for interaction). Property names are Richardson's "magic strings": they matter because humans, working on behalf of machines, must understand them. Shared, published vocabularies (and standards like RDF, JSON-LD, schema.org) close the "semantic gap" across independently built services. This parallels Evans's "ubiquitous language" in DDD, but applied across codebases rather than within one.

### Idempotency
An operation is idempotent if performing it multiple times has the same effect as performing it once. GET, HEAD, PUT, DELETE, and OPTIONS are idempotent; POST is not (each call creates a new entity). PATCH is not idempotent by default but can be implemented to be. Idempotency improves fault tolerance: a client can safely retry after a timeout. Idempotency keys (client-supplied identifiers) let even create operations be made safely repeatable.

## Frameworks & models

### The REST constraints (Fielding)
- **Client-server** - separation of concerns; client and server evolve independently as long as the interface holds. The client needs only the resource address.
- **Stateless** - each request carries all the context the server needs; the server stores no session. Induces visibility, reliability, and scalability (easy load balancing, easy recovery).
- **Cacheable** - responses must declare themselves cacheable or not (explicitly via headers, implicitly via HTTP semantics). GET is cacheable by default; POST is not; PUT/DELETE are not. Cacheable responses should carry a validator (ETag or Last-Modified). Caching reduces bandwidth, latency, and server load and hides network failures.
- **Uniform interface** - the unifying constraint, with four sub-principles: identification of resources (URIs); manipulation through representations; self-descriptive messages (each message says how to process it); and HATEOAS.
- **Layered system** - intermediaries (caches, proxies, load balancers, gateways, firewalls) can sit between client and server transparently. Enables scalability and security but adds latency and complexity. Most modern APIs have an API gateway and load balancer in this layer.
- **Code on demand (optional)** - servers may send executable code (scripts) to extend the client. Rare in practice; increases client-server coupling.

### Richardson Maturity Model (RMM)
Assesses how closely an API conforms to REST by examining URIs, HTTP methods, and hypermedia controls. Four levels:
- **Level 0 - The Swamp of POX**: a single endpoint, all operations via POST, operation named in the payload, always returns 200. SOAP and (notably) GraphQL fall here by this metric. Not RESTful.
- **Level 1 - Resources**: distinct URIs per resource, but still POST-for-everything and 200-for-everything (typical RPC). Often action-oriented URIs (/purchase-order/start).
- **Level 2 - HTTP Verbs**: resource-specific URIs, correct HTTP methods, meaningful status codes. This is what the industry usually calls "RESTful." Most APIs live here. Lacks hypermedia.
- **Level 3 - Hypermedia Controls (HATEOAS)**: Level 2 plus embedded links/forms so clients discover related resources and actions dynamically. The "holy grail"; harder to build but most decoupled and evolvable.

### Web API Design Maturity Model (WADMM, Amundsen)
Complements RMM by assessing the data/document model rather than HTTP usage. Four levels:
- **Level 0 - Database-centric**: API exposes raw DB rows including internal IDs and security fields. Tightly couples API to the database; cheap but fragile.
- **Level 1 - Object-centric**: exposes the in-memory object model. Better than raw DB but still leaks implementation details and lacks a consumer model.
- **Level 2 - Resource-centric**: an external resource abstraction decoupled from internal models. What most people associate with REST; good tooling (OpenAPI). Risk: still often a CRUD mirror of internal data.
- **Level 3 - Affordance-centric**: a fully external model built around use cases, messages, and actions rather than CRUD over resources; can serve any format/protocol/workflow the client needs.

### API style families and selection (Jarzyna & Amzani)
- **Function-based** (RPC, SOAP, gRPC): operations as verbs; good for actions, transactions, low-latency internal calls. Low maturity by RMM.
- **Resource-based** (REST, OData): manipulate noun resources via HTTP; the default for CRUD-style and broadly adopted (REST ~86% adoption per Postman 2023).
- **Query-based** (GraphQL): client specifies exactly the fields/relationships it needs; eliminates over- and under-fetching; ideal for diverse clients (mobile/web), nested data, CMSs, SPAs. Harder caching and security; no native hypermedia.
- **Event-based** (Webhooks, WebSockets, Kafka/RabbitMQ/SNS-SQS with AsyncAPI): asynchronous, real-time push; ideal for notifications, streaming, real-time updates; error handling via dead-letter queues, retries, circuit breakers.
Selection questions: What is the API's primary function (action / resource / data retrieval / real-time)? Who are the consumers (including AI agents)? What are the performance/latency requirements? How dynamic and client-specific is the data? Mixing styles in one architecture is common and recommended (REST for CRUD + GraphQL for flexible reads + Webhooks for events).

### API methodology (Patni, agile strategy)
Five phases: domain analysis / API description -> architecture design (protocol, endpoints, URI design, security, performance; then resources, representations, content types, parameters, methods, status codes, consistent naming) -> prototyping (high-fidelity, with simulation/stubs, acceptance test with pilot consumers) -> implementation (full back-end integration, NFRs) -> publish (ownership moves from dev to operations; further change goes through change management). Four design starting strategies: bolt-on (add API to existing app), greenfield/API-first (start from scratch, simulate back end), agile (start without full specs, iterate), facade (middle ground; shape existing systems behind an ideal interface).

## Patterns

- **Hypermedia (HATEOAS) responses** - Intent: embed links/forms so clients discover next actions at runtime. When to use: APIs that must evolve without breaking clients, discoverable pagination/state transitions, and increasingly AI/LLM consumers. Trade-offs: harder to design (every representation must advertise related resources and respect access control) and harder for clients to consume; pays off in decoupling and longevity.
- **Semantic profile / published vocabulary** - Intent: describe the problem space and terms of an API in a machine-readable profile (ALPS, JSON-LD, schema.org) separate from the wire format. When to use: cross-organization interoperability, machine clients. Trade-offs: extra artifact to maintain; enables runtime profile negotiation.
- **Idempotent action / client-supplied identifier** - Intent: make writes safely repeatable by accepting a client-generated key or modeling the action idempotently. When to use: unreliable networks, create operations, retry logic. Trade-offs: server must dedupe/store keys; greatly improves reliability and throughput.
- **Standardized error reporting (Problem Details, RFC 9457)** - Intent: a consistent machine-readable error body (type, title, status, detail, instance) using application/problem+json. When to use: every API. Trade-offs: minor extra structure; huge gains in consumer experience, tooling, and time-to-integrate. Extendable with custom fields.
- **Evolution-only (additive change) with Postel's law** - Intent: evolve a single version by only adding optional things and never removing/changing existing ones; clients ignore unknown fields. When to use: when stability and a single shared version are priorities. Trade-offs: requires strict discipline; breaking changes force a new resource variant (new URI) instead of mutating the old one.
- **Resource-based versioning** - Intent: version individual resources independently (URI /products/v2 or media type vnd.example.products.v2+json) rather than the whole API. When to use: large APIs needing granular, incremental upgrades. Trade-offs: more documentation and coordination overhead; risk of inconsistency.
- **Hybrid versioning model** - Intent: evolve non-breaking changes in place, introduce a new version only for breaking changes (Stripe's model: semi-annual major releases plus monthly backward-compatible updates). When to use: most public APIs balancing innovation and stability. Trade-offs: must run the deprecation/sunset machinery well.
- **Deprecation and sunsetting** - Intent: a transparent end-of-life lifecycle signaled at runtime (Sunset header per RFC 8594, Deprecation header, Link header to migration guide) and in documentation (badges, notes, timelines). When to use: any breaking change or retirement. Trade-offs: ongoing communication and monitoring; prevents calcification and decay and preserves trust.
- **Stable URL / preventing internal model leaks** - Intent: publish at least one stable entry URL and convert internal models to external messages so storage internals never leak. When to use: every public-facing service. Trade-offs: a mapping/translation layer to maintain; protects clients from internal change.
- **API facade** - Intent: present a simple, self-consistent interface over a complex or legacy subsystem; design the ideal API, implement with data stubs, then mediate to back-end systems. When to use: modernization, partner-facing APIs over messy internals. Trade-offs: an extra layer; decouples consumers from internal complexity.
- **Partial response / field selection** - Intent: let clients request only the fields they need (?fields=...). When to use: bandwidth-constrained or diverse clients. Trade-offs: server-side parsing complexity; reduces over-fetching without going full GraphQL.
- **Pagination (offset vs cursor)** - Intent: navigate large collections predictably. Offset/limit suits stable data; cursor/keyset suits real-time, frequently-changing, large datasets. When to use: any collection resource. Trade-offs: offset is simple but drifts when data changes mid-browse; cursor is robust but more complex. Provide self/next/prev links and collection metadata (size, totalElements, totalPages, number).
- **Content negotiation / multiple formats** - Intent: serve and accept multiple representations driven by Accept/Content-Type headers. When to use: heterogeneous consumers (web JSON, data-science CSV, legacy XML). Trade-offs: more mappings; broad reach.

## Principles & best practices
- Keep base URLs simple and intuitive: two per resource - a collection (/customers) and an element (/customers/1). Keep verbs out of URLs; let HTTP methods carry the verb. Use plural, concrete nouns. Express relationships by nesting (/customers/5678/podcasts). Push optional state/filters behind the query string ("sweep complexity under the ?").
- Use HTTP semantics correctly: GET (read), POST (create/non-idempotent), PUT (replace), PATCH (partial update, via JSON Patch RFC 6902 or JSON Merge Patch RFC 7396), DELETE, HEAD, OPTIONS.
- Design representations as messages, not database rows. Decouple the external contract from internal models (aim for WADMM Level 2+).
- Standardize errors. Map outcomes to three families - success (2xx), client error (4xx), server error (5xx) - and start from a small set (200, 400, 500; then 201, 304, 404, 401, 403, 429). Document every error code your API can return.
- Always version; make it mandatory. A pragmatic rule: never ship without a version; maintain at least one prior version and give consumers at least one cycle to migrate. Avoid hardcoding v1 in the base URL when you want the default to track latest stable.
- Validate inputs at runtime; ignore unknown fields on input (robustness/Postel's law) while emitting strictly conformant output.
- Codify operational policy (rate limits via 429, SLAs, auth) in the API contract (OpenAPI/AsyncAPI). Use examples liberally in the spec - they drive mocks, tests, client generation, and developer understanding.
- Prefer individual requests over bulk operations that create distributed transactions; offer async processing for genuine bulk needs.
- Adopt standards where they fit: SCIM for identity, HAL for hypermedia JSON structure, Problem Details for errors, OpenAPI for REST contracts, AsyncAPI for event APIs, Arazzo for multi-step workflows. Singular standards speed integration; global standards give uniform experience but can over-constrain.

## Anti-patterns & pitfalls
- The Swamp of POX / RPC-over-HTTP: one endpoint, POST for everything, always 200. Breaks caching, visibility, and HTTP semantics.
- Database-centric or object-centric APIs that leak internal IDs, schema versions, and security fields - they couple consumers to your storage and break them on every internal change.
- Hardcoding URLs and assuming fixed message schemas in clients instead of following hypermedia and binding to media types - makes clients brittle to server change.
- Versioning everything for non-breaking changes, causing needless fragmentation; or never versioning and silently breaking consumers.
- Deprecating without runtime signals or migration guidance: "if a client can still integrate with a deprecated feature without a clear warning, the deprecation has failed."
- Treating HATEOAS as all-or-nothing and skipping it entirely; partial hypermedia (links for pagination/relations) still adds value.
- Over-fetching/under-fetching forcing many round-trips; address with field selection or a query-based style where warranted.
- Custom headers and invented conventions when established HTTP headers and standards exist - hurts tooling interoperability.
- Bulk operations spanning services that create distributed transactions and messy partial-failure semantics.

## Decision guidance
- Choose the style by interaction model first: actions/transactions -> function-based (gRPC for low latency); CRUD over entities -> REST; flexible client-driven reads -> GraphQL; real-time push -> event-based. Mix styles per concern in one system.
- Target RMM Level 2 as the baseline for most APIs; add Level 3 hypermedia where evolvability, discoverability, or AI/agent consumption matters and you can afford the client complexity.
- Decouple the contract from internals (WADMM Level 2+) before optimizing anything else; this is the single biggest driver of long-term stability.
- Default to evolution-only/additive change; reach for versioning only for genuine breaking changes, and prefer the hybrid model for public APIs. Whatever you choose, pair it with explicit deprecation/sunset signaling.
- For collections, pick pagination by data volatility (offset for stable, cursor for real-time/large). For partial data needs, prefer field selection before adopting GraphQL.
- Make every write either naturally idempotent or idempotent via client-supplied keys so clients can retry safely.
- Standardize errors with Problem Details and codify rate limits, auth, and SLAs in the machine-readable contract from day one.
- Design "on the scale of decades": assume strangers, change, and a long life. Favor the least powerful sufficient technology, shared vocabularies, and stateless, self-descriptive messages.

## Source attribution
- REST roots (Fielding, Berners-Lee, Kay), hypermedia, affordances, vocabularies/magic strings, the umbrella principle, and the design/client/service/data/workflow recipe families: Amundsen (2022), Part I and the recipe catalog.
- REST constraints in depth, RMM and WADMM with leveled examples, HTTP protocol/methods/idempotency, JSON Patch/Merge Patch, API style families and selection, OpenAPI contracts, Problem Details, pagination, versioning (global/resource/evolution-only/hybrid, Stripe case), and deprecation/sunset headers: Jarzyna & Amzani (2025), Ch 5-6, 8-13.
- SOAP vs REST comparison, REST fundamentals and constraints, content negotiation, API design strategies and five-phase methodology, API modeling tools (RAML/OpenAPI/Blueprint), and the practical best-practices set (URLs, errors, versioning, partial response, pagination, facade): Patni (2023), Ch 1 and 4.

## Recent developments (2024–2026)

- **AI agents became a first-class API consumer, and MCP emerged as the de facto standard for tool access.** Anthropic's Model Context Protocol (MCP), a JSON-RPC-based protocol designed for LLM-to-tool interaction with schema discovery, typed I/O, and streaming, went from launch (late 2024) to near-universal adoption: by 2026 it was supported across Claude, ChatGPT, OpenAI, Google DeepMind, Microsoft, and AWS, with tens of millions of monthly SDK downloads and 10,000+ public MCP servers. This validates the file's thesis that APIs must now be designed for "AI agents you have never met," and adds a new design target alongside REST/gRPC/GraphQL. (n1n.ai 2026; truthifi.com 2026; Wikipedia, "Model Context Protocol")
- **The consensus settled on hybrid, multi-protocol stacks rather than a single winner.** Typical 2026 surveys show ~65-80% of teams using REST for public/partner APIs, ~28-40% piloting GraphQL for complex internal composition, and ~25% using gRPC for low-latency service-to-service traffic. REST with good caching still beats GraphQL by ~20-35% on repeat endpoints; gRPC remains 5-10x faster for internal calls. The recommended pattern: REST at the edge, GraphQL for data-graph composition, gRPC internally, MCP for agent orchestration. (Java Code Geeks 2026; designgurus.io; dasroot.net 2026)
- **OpenAPI 3.2.0 (Sept 2025) broadened the spec beyond classic REST.** It added structured tag nesting, native streaming media types (SSE, JSON Lines, JSON Sequences), the QUERY HTTP method, and OAuth 2.0 Device Authorization Flow, and the OpenAPI Initiative is extending coverage toward gRPC and GraphQL descriptions. This strengthens the "codify the contract in a machine-readable spec" best practice. (Nordic APIs 2025; API7.ai 2026; The New Stack)
- **Arazzo moved from "one to watch" to a real workflow-description standard.** The OpenAPI Initiative's Arazzo Specification (a language-agnostic way to express multi-step API call sequences and dependencies over OpenAPI/AsyncAPI descriptions) gained tooling and roadmap commitments across vendors, making the file's existing mention of Arazzo more concrete. It is increasingly relevant for describing agent/multi-step workflows. (OpenAPI Initiative; Nordic APIs 2025)
- **GraphQL Federation became an open, vendor-neutral standard, with a notable router shift.** Federation spec work defined neutral composition/distributed-execution rules so any compliant router works, and the ecosystem shifted away from a single vendor: WunderGraph Cosmo usage rose sharply while Apollo GraphOS share fell in 2024 surveys. tRPC also gained traction in TypeScript codebases for end-to-end type safety without GraphQL's overhead. (WunderGraph 2026; thebetterwebmovement.com)
- **AI-powered API gateways are the headline 2026 gateway trend.** Gateways that auto-generate documentation, enforce linting/governance policies (tools like SpecsGPT), handle rate limiting, and detect anomalies are emerging, alongside tighter gateway/service-mesh integration for centralized governance and tracing. (n1n.ai 2026; Java Code Geeks 2026)
