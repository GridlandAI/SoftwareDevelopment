# System Design Method

**Domain:** 07 - Distributed Systems, Scalability, and Resilience
**Primary sources:** Acing the System Design Interview (Tan, 2024); System Design Guide for Software Professionals (Sinha & Chopra, 2024)

## Overview

A repeatable system-design method gives an engineer a disciplined way to take a vague prompt ("design a service like Twitter") and arrive at a defensible architecture under time pressure. The method matters far beyond interviews: it is the same sequence used to scope a real greenfield service or a major rearchitecture. Its value is in the order of operations (requirements before APIs, APIs before data model, data model before scaling) and in making every decision quantitative and trade-off-aware rather than naming technologies by reflex.

This topic distills the design loop from Tan and Sinha & Chopra: clarify requirements and scope, do back-of-the-envelope estimation, assemble building blocks (load balancers, caches, CDNs, SQL/NoSQL databases, queues, search, object storage), draft the API and data model, then iterate on scaling and bottleneck analysis while explicitly discussing trade-offs. It pairs with `scalability-principles.md`, which supplies the scaling techniques (the AKF Scale Cube, statelessness, replication, sharding) that this method applies.

## Core concepts

**Functional vs. non-functional requirements.** Functional requirements state what the system does (post a tweet, follow a user, search). Non-functional requirements state how well it must do it: scalability, availability, fault tolerance, performance/latency and throughput, consistency, accuracy, complexity/maintainability, cost, security, and privacy. Tan's rule: every requirement, functional and non-functional, must carry a number (how many items, in how many milliseconds, for how many daily active users).

**Scope discipline.** You cannot design everything in the time available, so explicitly mark common endpoints (health, signup, login) as out of scope, brainstorm the functional requirements as a quick list (do not enumerate one at a time and risk missing some), and confirm with the stakeholder which are in and out before going deep.

**High-level vs. low-level design (Sinha & Chopra).** High-level design fixes the architecture: components, modules, data flow (ingestion, storage, processing, retrieval), and how the non-functional attributes (scalability, maintainability, reliability, latency) are met. Low-level design fills in algorithms, data structures, concrete APIs, and code optimizations within each component. Do high-level first; descend selectively into the components that carry the most risk or load.

**Back-of-the-envelope estimation.** Translate user numbers into load and capacity. From daily active users and per-user actions, derive daily and then per-second request rates (QPS), storage per item times volume times retention, and bandwidth as deliveries times payload size. These numbers decide whether data fits on one host, whether you need sharding, how big the cache must be, and where the bottlenecks will be.

## Frameworks & models

### The design loop (Tan)

A system-design discussion is dynamic, but the canonical order is:
1. **Clarify requirements and discuss trade-offs.** Functional first (within ~10 minutes / ~20% of the time), then non-functional. Identify user categories and roles (manual vs. programmatic, consumer vs. enterprise, technical vs. non-technical), attach numbers to everything, and ask "are there other requirements?" rather than waiting to be told.
2. **Draft the API specification.** From the functional requirements, sketch the GET/POST/PUT/DELETE endpoints with path and query parameters. Keep it to a few minutes; a rough scribble is fine, note that a formal OpenAPI or GraphQL schema would follow. Drafting the API often surfaces missed requirements.
3. **Design the data model.** Define entities, relationships, keys, and indexes. Note where denormalization avoids costly JOINs, and where data should live (SQL vs. NoSQL vs. object store). Discuss possible analytics.
4. **Discuss failure design and operations.** Graceful degradation, monitoring and alerting, bottlenecks, load balancing, removing single points of failure, high availability, disaster recovery, and caching.
5. **Discuss complexity, trade-offs, cost, maintenance, and decommissioning.**

### Estimation worked example (Sinha & Chopra, Twitter)

Given assumptions (100M total users, 20M daily active, 5 tweets/user/day, 200 B/tweet, 1 MB media on 20% of tweets, 100 followers/user, 5-year retention):
- **Tweet storage:** 20M x 5 x 200 B = 20 GB/day -> 7.3 TB/year -> ~36.5 TB over 5 years.
- **Media storage:** 20M x 5 x 20% x 1 MB = 200 TB/day -> 73 PB/year -> ~365 PB over 5 years (media dominates total storage).
- **Bandwidth:** tweet delivery 20M x 5 x 100 followers = 10B deliveries/day x 200 B = 2 TB/day; media ~2 PB/day.
- **Processing:** ~1,200 tweets/second peak; ~240 media uploads/second; timeline **fan-out ~120,000 requests/second** (100 followers x 1,200 tweets/s).
- **Cache sizing:** 2B daily tweet views x 80% hit rate x 200 B ~= 320 GB.

These numbers immediately justify distributed storage, an object store for media, a CDN, a message queue for fan-out, and a sizeable distributed cache, before any technology is named.

### Building blocks catalog

The reusable components an architect composes:
- **Load balancer.** Distributes traffic across horizontally scaled hosts. L4 (transport/TCP) routes on address info only and just forwards packets; L7 (application/HTTP) inspects content and can route on it, authenticate, and terminate TLS. Sticky sessions pin a client to a host for stateful services (duration-based or application-controlled cookies). A reverse proxy differs in intent: it manages client–server communication (caching, compression, SSL termination), whereas a load balancer exists for scalability. (Tan)
- **Cache.** In-memory tier (e.g., Redis) holding hot data. Read strategies (cache-aside / read-through) and write strategies (write-through, write-back) trade consistency against latency. Eviction policies such as LRU keep the working set hot. Requires an invalidation strategy and cache warming. (Tan; Sinha & Chopra)
- **CDN.** Geographically distributed edge caches for static and media content, cutting latency and origin load. Trade-offs include cache-invalidation lag and cost; consistent hashing distributes content across edge nodes. (Tan; Sinha & Chopra)
- **SQL database.** Strong consistency, ACID transactions, rich queries and JOINs; scales reads via leader/follower replication and writes via sharding (harder). Default for relational, transactional data. (Tan)
- **NoSQL database.** Key-value (DynamoDB), wide-column (Cassandra, HBase), and document stores; scale horizontally with consistent hashing and tunable consistency; ideal for high write throughput, large volumes, and simple access patterns. (Sinha & Chopra)
- **Object store.** Scalable, durable storage for large blobs (images, video) such as S3 or Azure Blob; the database stores a reference (address) to the object. (Tan; Sinha & Chopra)
- **Message queue / pub-sub.** Asynchronous, decoupled communication (Kafka, RabbitMQ). Enables fan-out, ETL, and burst absorption; provides loose coupling at the cost of eventual consistency and ordering/duplicate handling. Kafka favors high-throughput durable streams; RabbitMQ favors flexible routing. (Tan; Sinha & Chopra)
- **Search.** Inverted-index search (Elasticsearch/Lucene) for full-text and ranked queries, kept separate from the primary store and fed asynchronously. (Tan; Sinha & Chopra)
- **Supporting services.** API gateway (entry point: routing, auth, rate limiting, transformation), service discovery, metadata service, service mesh / sidecar, and observability (logging, metrics, tracing, alerting via Prometheus/Grafana/ELK). (Sinha & Chopra; Tan)

### Distributed-systems theory the method draws on (Sinha & Chopra)

- **CAP / PACELC.** Under a network partition you trade consistency for availability; PACELC adds that even without a partition you trade latency for consistency.
- **Quorum tuning.** With N replicas, reading R and writing W: R+W > N gives strong consistency, otherwise eventual. Write options range from serial-sync (high latency, strong) to write-to-Kafka-then-ack (lowest latency, highest throughput).
- **Consistent hashing.** Maps keys and nodes onto a ring so adding or removing a node remaps only a fraction of keys; virtual nodes smooth distribution. The backbone of sharded databases, distributed caches, and CDNs.
- **Consensus and supporting structures.** Paxos/Raft for agreement; Bloom filters for cheap probabilistic membership tests; HyperLogLog for cardinality estimation; count-min sketch for approximate frequency counts in streaming aggregations.

## Patterns

Also recorded in `../_indexes/PATTERN-CATALOG.md`.

**Requirements-first design loop** - Intent: reach a defensible architecture by clarifying scope and numbers before drawing components. When to use: any new service or major rearchitecture, and every design interview. Trade-offs: prevents wasted detail and missed requirements; feels slow if the prompt seems obvious.

**Back-of-the-envelope estimation** - Intent: convert user counts into QPS, storage, and bandwidth to size the system and locate bottlenecks. When to use: immediately after requirements, before choosing technologies. Trade-offs: cheap and decisive about distribution/sharding/caching; only as good as its assumptions, so state them.

**Timeline fan-out (push vs. pull)** - Intent: deliver feed items to followers either by writing into each follower's timeline on post (fan-out-on-write / push) or assembling the timeline at read time (fan-out-on-read / pull). When to use: push for users with modest follower counts (fast reads); pull or a hybrid for celebrities with huge follower counts to avoid write amplification. Trade-offs: push gives fast reads but heavy writes (~120k req/s at Twitter scale); pull gives cheap writes but slow reads; hybrid balances both.

**API gateway** - Intent: a single entry point handling routing, authentication, rate limiting, and request/response transformation. When to use: any microservices system with external clients. Trade-offs: centralizes cross-cutting concerns; can become a bottleneck or single point of failure if not scaled and isolated.

**Rate limiting (token/leaky bucket, window counters)** - Intent: cap a client's request rate to protect downstream services. When to use: public APIs and shared services. Trade-offs: token bucket allows bursts up to a cap; leaky bucket smooths to a constant rate; fixed-window is simple but allows edge bursts; sliding-window log is accurate but memory-heavy; sliding-window counter approximates accuracy cheaply. Counts live in an in-memory store (Redis); deploy as a sidecar or a shared service with a rules service.

**Denormalization for read performance** - Intent: duplicate data into a single table/document so common reads avoid JOINs. When to use: read-heavy access patterns with known queries. Trade-offs: fast reads; write amplification and consistency upkeep.

## Principles & best practices

- **Attach a number to every requirement.** "Fetch news items" is incomplete without how many, how fresh, in how many milliseconds.
- **Requirements -> API -> data model -> scaling, in that order.** Each step constrains and validates the next; drafting the API exposes missing requirements, the data model exposes access patterns, access patterns drive the scaling choices.
- **State assumptions out loud and design to them.** Estimation is only meaningful with explicit assumptions; revise the design if assumptions change.
- **Do not over-engineer.** Tan repeatedly notes that an "obvious" strongly-consistent or fully-replicated design may be overkill; match the design to the stated non-functional requirements and cost.
- **Make the load balancer implicit.** It is understood to sit in front of every horizontally scaled service; do not clutter diagrams with it (Tan).
- **Push large blobs to object storage and a CDN; keep references in the database.** Media dominates storage and bandwidth; never store blobs in the primary database.
- **Use a queue to decouple and absorb bursts.** Asynchronous fan-out, ETL, and indexing keep request paths fast and services loosely coupled.
- **Always include logging, monitoring, and alerting.** They are nearly always mandatory; most cloud vendors make them trivial to enable.

## Anti-patterns & pitfalls

- **Naming technologies before requirements.** Jumping to "use Cassandra and Kafka" without numbers or access patterns produces an unjustified design.
- **Skipping estimation.** Without QPS and storage math you cannot tell whether one host suffices or sharding is mandatory; you guess at cache and bandwidth sizing.
- **Lingering on the API or data model.** Spending too long here starves the scaling and failure discussion, which is where the design is really tested.
- **Letting the stakeholder do the thinking.** Waiting to be handed requirements signals weak ownership; brainstorm and confirm instead.
- **Mismatched consistency choice.** Strong consistency where eventual would do wastes availability and latency; eventual where correctness is required (banking) is a defect.
- **Ignoring the celebrity/hot-key problem in fan-out.** A pure push model collapses under users with millions of followers; plan a hybrid.
- **Single points of failure.** Any un-replicated component (gateway, leader DB, cache) without N+1 undermines the stated availability.

## Decision guidance

Walk the loop in order and let constraints select the building blocks:
1. **Clarify** functional then non-functional requirements; attach numbers; mark scope.
2. **Estimate** QPS, storage (per item x volume x retention), and bandwidth. If data exceeds one host -> plan sharding/distributed storage. Size the cache from view volume x hit rate x item size.
3. **Draft the API**, then the **data model**; decide SQL (transactional, relational, JOIN-heavy) vs. NoSQL (high write volume, simple access, horizontal scale) vs. object store (blobs) per access pattern; denormalize for known read paths.
4. **Choose consistency** via R/W vs. N: strong (R+W>N) for correctness-critical writes, eventual for availability and latency.
5. **Insert building blocks** to meet non-functionals: load balancer + stateless services for scale; cache + CDN for latency; queue for decoupling and bursts; search index fed asynchronously; API gateway for cross-cutting concerns.
6. **Analyze bottlenecks** (e.g., fan-out write amplification, hot keys, leader-write limits) and apply the matching remedy (hybrid fan-out, consistent hashing, read replicas, sharding).
7. **Add operations**: monitoring, alerting, graceful degradation, DR, and rate limiting; then weigh complexity and cost.

Worked case studies covered by the sources to reason from: Craigslist, rate limiter, notification/alerting, autocomplete/typeahead, Flickr, CDN, text-messaging/chat, Airbnb, news feed, top-K dashboard (Tan); Twitter, Instagram, Google Docs, Netflix, key-value store, distributed cache, distributed queue, pub/sub (Sinha & Chopra).

## Source attribution

- The design loop (clarify -> API -> data model -> failure/ops -> complexity), functional vs. non-functional requirements with numbers, scope discipline, load-balancer concepts (L4/L7, sticky sessions, reverse proxy), caching read/write strategies and invalidation, rate-limiting algorithms, worked case studies (Craigslist, rate limiter, news feed, chat, etc.): **Tan, Acing the System Design Interview**, Chs. 2–4, 6 (API paradigms), 7–17.
- High-level vs. low-level design, building blocks (key-value store, distributed cache, distributed queue, pub/sub, API/security/metrics, object storage), CAP/PACELC, quorum (R+W>N) and read/write options, consistent hashing, Bloom filter, HyperLogLog, count-min sketch, and the Twitter/Instagram/Google Docs/Netflix estimation and fan-out case studies: **Sinha & Chopra, System Design Guide for Software Professionals**, Chs. 1–3, 5–8, 11–14.

## Recent developments (2024–2026)
- **AI system design is now a distinct track within the method.** 2026 guides add an LLM/AI-application design loop on top of the classic one: orchestration layers, retrieval (RAG) pipelines, vector stores, guardrails, evaluation/observability, and explicit cost management for nondeterministic, latency-heavy inference. Estimation now includes tokens, GPU capacity, and inference cost alongside QPS, storage, and bandwidth. (systemdesignhandbook.com, "AI System Design: A Complete Guide (2026)", https://www.systemdesignhandbook.com/guides/ai-system-design/; infrasketch.net, "LLM System Design Architecture", 2025, https://infrasketch.net/blog/llm-system-design-architecture)
- **New building blocks: vector database, semantic cache, model-serving tier.** The building-blocks catalog has effectively grown to include vector indexes (semantic search/RAG), semantic caching (reuse responses for similar queries), and disaggregated inference serving, which sit beside load balancers, caches, CDNs, queues, and search. (gitnexa.com, "AI Application Architecture Guide 2026", https://www.gitnexa.com/blogs/ai-application-architecture; runpod.io, "AI Model Serving Architecture", 2025, https://www.runpod.io/articles/guides/ai-model-serving-architecture-building-scalable-inference-apis-for-production-applications)
- **Cost-aware design is more prominent.** With AI workloads, batching requests for lower per-token pricing and semantic caching to avoid recompute are now standard back-of-the-envelope considerations, reinforcing the method's "do not over-engineer / state assumptions" discipline. (arXiv/industry inference-cost coverage, 2025-2026)
- **The requirements-first loop itself is unchanged and still taught.** Clarify -> estimate -> API -> data model -> scaling -> ops, with a number on every requirement, remains the canonical sequence in interview prep and real design; AI simply adds new constraints to estimate and new components to compose. (Tan, Sinha & Chopra method, reaffirmed in 2025-2026 system-design guides)
- **Classic distributed-systems theory persists.** CAP/PACELC, quorum tuning (R+W>N), consistent hashing, and probabilistic structures (Bloom filter, HyperLogLog, count-min sketch) are still the theory the method draws on; no foundational change in this period.
