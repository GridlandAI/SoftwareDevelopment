# DevMentor Instruction Manual

A complete guide to the DevMentor plugin: what it is, how to install it, how each skill and command works, and how to combine the agents to design, build, and architect software more effectively.

---

## 1. What DevMentor is

DevMentor is a suite of specialist advisors that cover the entire software development lifecycle **beyond the coding itself**. Where most coding assistants help you write lines of code, DevMentor helps you make the decisions that determine whether those lines add up to a good system: what to build, how to specify it, how to architect it, which patterns to apply, how to scale and secure it, how to test it, and how to document and deliver it.

It is built as one orchestrator plus fourteen specialists. Each specialist owns a slice of the lifecycle and carries its own bundled knowledge base. The orchestrator (the hub) understands a request, routes it to the right specialist, and stitches specialists together when a problem spans several of them.

DevMentor is grounded in a knowledge base distilled from **243 books and papers** on software development, then enriched with web research current to **2024 through 2026**. Each topic file inside a skill follows the same structure: Overview, Core concepts, Frameworks and models, Patterns, Principles and best practices, Anti-patterns and pitfalls, Decision guidance, Source attribution, and a "Recent developments (2024-2026)" section. This means the advice is both rooted in the field's canonical thinking and aware of how that thinking has shifted recently (for example, the modular-monolith resurgence, the standardization of the Model Context Protocol, the open table-format convergence, and the empirical findings on AI-assisted productivity).

### What makes it different

- **Decision-first, not code-first.** It optimizes for the choices and artifacts that shape a system: specifications, decision records, trade-off analyses, system designs, test strategies, modernization plans.
- **Grounded and cited.** Every specialist reasons from real source material and names the frameworks and sources it draws on, rather than improvising.
- **Trade-off honest.** It surfaces the costs of each option and the conditions under which each wins, instead of selling a single "best practice".
- **Clarification-first.** It asks focused questions before giving a long answer, because the right architecture depends on your actual constraints.

---

## 2. The full contents

**1 orchestrator + 14 specialist skills:**

| Skill | Concern |
|-------|---------|
| `devmentor-hub` | Orchestrator: understands the need, routes to a specialist, synthesizes across them. |
| `devmentor-requirements` | Requirements, specifications, user stories, BDD, acceptance criteria. |
| `devmentor-product-discovery` | Discovery, MVPs, impact mapping, metrics, estimation, roadmaps. |
| `devmentor-architect` | Architecture fundamentals: quality attributes, trade-offs, ADRs, style selection. |
| `devmentor-architecture-patterns` | Microservices, event-driven, serverless, SOA, integration, APIs, micro frontends. |
| `devmentor-software-design` | DDD, design patterns, clean architecture, coupling and cohesion, refactoring. |
| `devmentor-distributed-systems` | Data-intensive design, scalability, resilience, system design, event sourcing/CQRS. |
| `devmentor-data-architecture` | Data modeling, mesh, lakehouse, pipelines, knowledge graphs, semantic AI. |
| `devmentor-cloud-platform` | Cloud-native, containers, Kubernetes, IaC, SRE, MLOps, platform engineering. |
| `devmentor-ai-systems` | LLM/GenAI and agentic design, MCP, multi-agent, securing GenAI, emerging tech. |
| `devmentor-ai-assisted-dev` | AI coding assistants, vibe coding, engineering discipline with AI. |
| `devmentor-enterprise-architecture` | EA frameworks, business architecture, modernization, agile enterprise. |
| `devmentor-quality-testing` | Test strategy, test design patterns, formal methods and verification. |
| `devmentor-ia-ux` | Information architecture, UX process, design psychology, UX for AI, frontend. |
| `devmentor-docs-delivery` | Documenting architecture, technical writing, Git and CI/CD delivery. |

**10 commands:** `/devmentor-spec`, `/devmentor-adr`, `/devmentor-architecture-review`, `/devmentor-recommend-patterns`, `/devmentor-tradeoff-analysis`, `/devmentor-system-design`, `/devmentor-tech-selection`, `/devmentor-test-strategy`, `/devmentor-modernization-plan`, `/devmentor-threat-model-genai`.

---

## 3. Installing DevMentor

The `devmentor` folder is both a plugin and a single-plugin marketplace, and each specialist is also shipped as a standalone `.skill` file. You have three ways to install.

### Option A: the whole suite as a plugin (recommended)

1. Unzip `devmentor-plugin-v1.0.0.zip` somewhere stable, or use the `devmentor` source folder directly.
2. Add it as a plugin marketplace, pointing at the `devmentor` folder (it contains `.claude-plugin/marketplace.json`).
3. Install the `devmentor` plugin from that marketplace.

This installs all 15 skills and all 10 commands together, with the knowledge base bundled in.

### Option B: individual skills

If you only want one or a few specialists, install the matching `.skill` files (for example `devmentor-architect.skill`). Each is self-contained: it carries its own knowledge slice and works without the rest of the suite. Use the "Save skill" action on the file.

### Option C: from source

Keep the `devmentor/` folder in your project. The skills live in `devmentor/skills/<name>/SKILL.md`, the commands in `devmentor/commands/`, and the manifests in `devmentor/.claude-plugin/`. Edit any description or instruction and repackage if you want to redistribute.

### Verifying the install

Ask a broad question such as "Help me think through the architecture for a new system" and confirm the hub responds and offers to route you. Or run a command, for example `/devmentor-adr should we use Postgres or DynamoDB for the orders service`, and confirm you get a structured decision record.

---

## 4. How to use DevMentor

There are three entry points. They overlap, so use whichever fits the moment.

### 4.1 Let the hub route you

When you are not sure which specialist you need, or your question spans several, just describe the situation. The hub figures out where it belongs.

> "We are about to build a B2B billing platform. I want to get the architecture right from the start but I am not sure where to begin."

The hub will clarify scope and constraints, then pull in the architect, the architecture-patterns specialist, and others as needed.

### 4.2 Go straight to a specialist

If you already know the concern, address it directly. The specialist descriptions are written so that natural phrasings trigger the right one.

> "Help me design the aggregates and bounded contexts for an order management domain." (triggers `devmentor-software-design`)

### 4.3 Run a command for a concrete deliverable

Commands are the fastest way to get a specific artifact. They take free-text arguments.

> `/devmentor-system-design a URL shortener handling 10k writes per second and 100M reads per day`

### Getting the best results

- **State constraints up front.** Scale, team size, deadlines, existing stack, compliance, budget, and risk tolerance all change the answer. The more you give, the less the agent has to ask.
- **Expect questions.** DevMentor asks before it assumes. Answer them; the second response will be far sharper than a guess would have been.
- **Ask for the artifact you want.** "Give me an ADR", "draw the component diagram as text", "produce a test plan", "write the spec". DevMentor prefers to hand you something concrete.
- **Push on trade-offs.** Ask "what breaks this", "when would the other option win", "what am I not considering". The knowledge base is built around exactly these questions.
- **Chain specialists deliberately.** Discovery, then requirements, then architecture, then patterns, then testing and delivery is a natural flow. You can also loop back when something downstream reveals a gap.

---

## 5. The orchestrator: devmentor-hub

**Role.** The hub is the front door. It does three jobs: understand what you actually need, route you to the right specialist, and synthesize across specialists when your problem is cross-cutting.

**When it engages.** Broad or ambiguous requests ("help me build", "where do I start", "review my approach", "I am designing a system"), or anything that clearly touches several specialties at once.

**Bundled knowledge (foundations and cross-cutting indexes).** The hub carries the lifecycle and process material plus the shared indexes:

- `knowledge/software-engineering-fundamentals.md`, `sdlc-and-process-models.md`, `agile-foundations.md`, `modern-product-engineering.md`, `project-troubleshooting-and-realities.md` - for framing the lifecycle, process choice, and delivery realities.
- `reference/PATTERN-CATALOG.md` - a consolidated index of hundreds of patterns across every domain, with the owning specialist for each.
- `reference/QUALITY-ATTRIBUTES.md` - a catalog of architectural characteristics and quality attributes.
- `reference/SOURCE-MAP.md` - the source books behind the knowledge base.

**How it behaves.** It will not guess at a long answer. It first establishes what you are building, what stage you are at, what decision or artifact you need, and your constraints. Then it either answers using the relevant specialist knowledge or names the best-fit specialist and proceeds in that mode.

**Example**

> **You:** We are a five-person team building an internal analytics product. I want to make sure we set ourselves up well technically. Where should we start?
>
> **Hub:** A few quick questions so I point you to the right place: (1) Do you already know what the product must do, or is the scope still fluid? (2) What is your delivery pressure, a demo in weeks or a durable platform? (3) Any fixed constraints, cloud, language, data residency? ... Based on your answers I would start you with product discovery to lock the smallest valuable scope, then requirements to make it testable, then the architect for a right-sized design. For a five-person team I will steer you firmly away from premature microservices.

**Routing map (abridged).** Architecture and structure to the architect; styles and integration to architecture-patterns; domain modeling and code design to software-design; scale and resilience to distributed-systems; specs and stories to requirements; what-to-build to product-discovery; data platforms to data-architecture; cloud and Kubernetes to cloud-platform; LLMs and agents to ai-systems; coding assistants to ai-assisted-dev; enterprise and modernization to enterprise-architecture; testing to quality-testing; IA and UX to ia-ux; documentation and delivery to docs-delivery.

---

## 6. The specialists in detail

Each specialist below lists its role, what it covers, its bundled knowledge files, the kinds of phrasing that trigger it, a worked example, and which other specialists it hands off to. They are ordered roughly as you would meet them across a project lifecycle.

### 6.1 devmentor-product-discovery - Product Discovery and Planning Advisor

**Role.** Helps you decide what to build and why, then plan delivery. This is where you go before writing requirements, to make sure you are about to build the right thing.

**Covers.** Continuous discovery and customer interviews; MVP scoping; impact mapping; product metrics and lean analytics; the One Metric That Matters; prioritization (MoSCoW, RICE, WSJF, Kano); estimation and forecasting; Shape Up shaping and betting; outcomes over outputs; roadmaps; futures thinking and opportunity sizing; AI product management.

**Bundled knowledge.** `continuous-discovery.md`, `lean-product-and-mvp.md`, `impact-mapping-and-outcomes.md`, `product-metrics-and-lean-analytics.md`, `shape-up-and-betting.md`, `estimation-and-planning.md`, `agile-product-management.md`, `pm-career-and-frameworks.md`, `futures-thinking.md`, `ai-product-management.md`, `project-initiation.md`.

**Trigger it with.** "scope an MVP", "run customer interviews", "build an impact map", "what metric should we track", "prioritize this backlog", "estimate this work", "Shape Up", "outcomes over outputs", "is this worth building".

**Example**

> **You:** We think we should build a recommendations feature. How do we decide if it is worth it before committing a quarter to it?
>
> **Advisor:** Let us frame it as an opportunity, not a feature. First, which business outcome are we moving (retention, average order value, activation) and what is the current baseline? Then I would build an impact map: goal -> the actors who can move it -> the behavior changes we need -> the smallest deliverables that could cause them. Before a quarter of build, test the riskiest assumption cheaply: a Wizard-of-Oz or fake-door test to see if anyone engages. I will give you the impact map skeleton and a one-week assumption test plan, plus the single metric that would tell you to continue or stop.

**Hands off to.** `devmentor-requirements` once the scope is chosen; `devmentor-architect` when feasibility or architecture shape affects what is worth building.

---

### 6.2 devmentor-requirements - Requirements and Specifications Analyst

**Role.** Turns a fuzzy idea into clear, testable requirements and specifications that a team can build against and a tester can verify.

**Covers.** The requirements engineering process (elicitation, analysis, specification, validation, management); functional vs non-functional requirements and requirement quality; user stories and INVEST; specification by example and BDD with Given-When-Then; acceptance criteria and the definition of done; PRDs and software requirements specifications; traceability and change control; living documentation.

**Bundled knowledge.** `requirements-engineering-process.md`, `requirements-types-and-quality.md`, `user-stories.md`, `specification-by-example-and-bdd.md`, `living-documentation.md`, `requirements-management-and-change.md`, `requirements-specification-and-design-bridge.md`, `requirements-for-systems-engineering.md`, `business-requirements-and-data.md`.

**Trigger it with.** "write user stories", "acceptance criteria", "draft a PRD", "apply INVEST or Gherkin", "this requirement is ambiguous", "define done", "requirements traceability", "specification by example".

**Example**

> **You:** Turn "users should be able to manage their notifications" into something we can actually build and test.
>
> **Analyst:** That is a theme, not a story, and "manage" is doing a lot of hidden work. Let me split it and make it testable. Epic: Notification preferences. Stories: choose channels per notification type; mute all for a period; per-type frequency. For the first story, acceptance criteria in Given-When-Then: Given a user with email and push enabled, When they disable push for "marketing", Then no marketing push is sent and email is unaffected, And the change takes effect within one minute. I will also flag the non-functional requirement hiding here (delivery latency) and add it explicitly.

**Hands off to.** `devmentor-quality-testing` to turn acceptance criteria into a test strategy; `devmentor-architect` when a non-functional requirement is architecturally significant; `devmentor-ia-ux` for the experience design behind a story.

---

### 6.3 devmentor-architect - Software Architect

**Role.** Helps you reason about architecture fundamentals: the quality attributes that matter, the trade-offs between options, and the decisions that lock them in. This is the center of gravity for "how should this system be shaped".

**Covers.** Architecture characteristics and quality attributes and how to prioritize them; trade-off analysis (the Hard Parts method); architecture decisions and ADRs; reversible vs irreversible decisions; coupling, cohesion, and connascence; components, modularity, and decomposition (including Lowy's volatility-based method); architecture style selection; risk-driven architecture; evolutionary architecture and fitness functions; architecture as code and automated governance; the architect role and facilitating decisions (the advice process).

**Bundled knowledge.** `architecture-characteristics-and-quality-attributes.md`, `trade-off-analysis-the-hard-parts.md`, `architecture-decisions-and-adrs.md`, `components-modularity-coupling-cohesion.md`, `architecture-styles-overview-and-selection.md`, `risk-driven-architecture.md`, `evolutionary-architecture-and-fitness-functions.md`, `architecture-as-code-and-governance.md`, `system-decomposition-the-method-lowy.md`, `facilitating-and-decentralizing-architecture.md`, `architect-role-and-soft-skills.md`, `architecture-design-methods.md`, `practical-architecture-process.md`, and more (19 files).

**Trigger it with.** "what architecture style", "identify quality attributes", "analyze the trade-offs", "write an ADR", "decompose this system", "fitness functions", "architecture review", "is this too coupled", "manage architectural risk".

**Example**

> **You:** We keep arguing about whether to go event-driven. How do we decide?
>
> **Architect:** Decide from drivers, not preferences. First, which characteristics dominate here, ranked? If it is responsiveness, extensibility, and fault isolation, event-driven scores well; if it is simplicity, strong consistency, and easy debugging, it scores poorly. Let me run the Hard Parts trade-off method: list the forces, score broker vs mediator topologies and a synchronous alternative against your top three characteristics, and state the conditions under which each wins. I will capture the outcome as an ADR so the reasoning survives, and note whether this is a one-way or two-way door (it is closer to one-way, so it deserves the rigor).

**Hands off to.** `devmentor-architecture-patterns` for depth on the chosen style; `devmentor-distributed-systems` for scale and resilience; `devmentor-software-design` for the internal design; `devmentor-docs-delivery` to document the architecture.

---

### 6.4 devmentor-architecture-patterns - Architecture Patterns Specialist

**Role.** Goes deep on architecture styles and integration once you know the shape you are reaching for. This is the catalog-level specialist for service-based and event-based systems and their APIs.

**Covers.** Microservices fundamentals and the full Richardson pattern catalog (decomposition, database-per-service, saga, API composition, CQRS, outbox, gateway/BFF, service discovery, deployment, observability, testing); event-driven architecture (broker vs mediator, choreography vs orchestration, eventual consistency); enterprise integration patterns; messaging and async (AMQP, RabbitMQ); REST and API design (Richardson maturity, HATEOAS, versioning, error formats); SOA and service design; micro frontends; production readiness; stack-specific realization (.NET, JVM/Spring, Python, TypeScript).

**Bundled knowledge.** `microservices-patterns-catalog.md`, `microservices-architecture-fundamentals.md`, `event-driven-architecture.md`, `enterprise-integration-patterns.md`, `messaging-and-async-communication.md`, `api-design-and-rest-patterns.md`, `soa-and-service-design.md`, `micro-frontends.md`, `microservices-production-readiness.md`, plus implementation and polyglot files (14 total).

**Trigger it with.** "microservices or monolith", "event-driven", "serverless", "saga or outbox", "API design", "service boundaries", "choreography vs orchestration", "micro frontends", "integration patterns".

**Example**

> **You:** Our microservices keep failing together when one is slow, and a single user action updates three services. Help.
>
> **Specialist:** Two distinct problems. The "fail together" symptom is a cascading failure across integration points; the fix is resilience at the boundary (timeouts, a circuit breaker, a bulkhead) which I will detail, plus back-pressure. The "one action, three services" symptom is a distributed transaction; do not use two-phase commit. Use a saga: choreographed if the steps are few and stable, orchestrated if the workflow is complex or needs central visibility, with the transactional outbox to publish events reliably. I will sketch the saga with its compensating actions and the idempotency keys you will need.

**Hands off to.** `devmentor-distributed-systems` for the deeper resilience and data-consistency theory; `devmentor-cloud-platform` to run the services; `devmentor-architect` when the choice reopens a fundamental trade-off.

---

### 6.5 devmentor-software-design - Software Design and Patterns Specialist

**Role.** Works at the level of modules, classes, and the domain model. Helps you express the problem domain clearly in code and keep the design changeable as it grows.

**Covers.** Strategic DDD (bounded contexts, context maps, subdomains, ubiquitous language); tactical DDD (aggregates, entities, value objects, domain events, repositories); GoF design patterns and when each earns its keep; Fowler's enterprise application patterns (domain model, transaction script, data mapper, unit of work); clean architecture and SOLID; coupling, cohesion, and connascence; Khononov's balancing-coupling model; empirical design and refactoring (Tidy First, code-as-crime-scene), craftsmanship.

**Bundled knowledge.** `domain-driven-design-strategic.md`, `domain-driven-design-tactical.md`, `domain-modeling-foundations.md`, `clean-architecture-and-dependency-rule.md`, `balancing-coupling.md`, `gof-design-patterns.md`, `enterprise-application-patterns.md`, `empirical-design-refactoring-and-craft.md`.

**Trigger it with.** "which design pattern", "model this domain", "bounded context", "aggregate design", "is this too coupled", "apply SOLID", "clean architecture vs layered", "refactor this class", "untangle a god class".

**Example**

> **You:** Our Order class is 1,200 lines and touches everything. How do I tame it?
>
> **Specialist:** That is a god class, and the coupling it radiates is the real cost. First, do not jump to patterns; find the seams. Use connascence to see what binds the responsibilities, and split along the strongest, most local connascence first. Likely you have several concepts hiding inside Order: the order lifecycle (an aggregate with invariants), pricing (a domain service or value objects), and persistence (a repository, not logic on the entity). I will propose the aggregate boundary and its invariants, move pricing to value objects, and apply the repository pattern, then suggest a Tidy-First sequence so each step is small and reversible.

**Hands off to.** `devmentor-architect` when module boundaries become architectural; `devmentor-architecture-patterns` when contexts map to services; `devmentor-quality-testing` to lock the refactor behind tests first.

---

### 6.6 devmentor-distributed-systems - Distributed Systems and Scalability Engineer

**Role.** The specialist for making systems scale and survive failure, and for working through a system design end to end.

**Covers.** Data-intensive application design (reliability, scalability, maintainability; data models; storage engines; replication; partitioning; transactions; consistency and consensus; batch and stream processing); the AKF scale cube; resilience and stability patterns (circuit breaker, bulkhead, timeouts, back-pressure, steady state, shed load); a repeatable system design method with estimation; reactive architecture; event sourcing and CQRS in depth.

**Bundled knowledge.** `data-intensive-systems.md`, `scalability-principles.md`, `resilience-and-stability-patterns.md`, `system-design-method.md`, `reactive-architecture.md`, `event-sourcing-and-cqrs.md`, `distributed-and-realtime-computing-foundations.md`.

**Trigger it with.** "how do I scale this", "shard or partition", "replication and consistency", "eventual consistency", "circuit breaker", "it falls over under load", "event sourcing vs CRUD", "CQRS", "design a system for".

**Example**

> **You:** Design a system for a notification service sending 50 million messages a day across email, SMS, and push.
>
> **Engineer:** Let me run the method. Requirements and scope first (fan-out from events, multi-channel, delivery guarantees, idempotency). Estimation: 50M/day is about 580/sec average, but notifications spike, so design for maybe 10x peak. Building blocks: an ingestion queue, a deduplication and idempotency layer keyed by event id, per-channel workers with provider rate limits, a dead-letter queue and retry with backoff, and a status store for delivery state. Consistency: at-least-once delivery plus idempotent consumers. I will lay out the components, the back-pressure strategy for provider throttling, and the failure modes, then a text diagram.

**Hands off to.** `devmentor-architecture-patterns` for the service and messaging patterns; `devmentor-cloud-platform` to run it; `devmentor-data-architecture` for the data stores.

---

### 6.7 devmentor-data-architecture - Data Architecture Advisor

**Role.** Designs how data is modeled, stored, moved, and governed, from warehouses and lakehouses to meshes, pipelines, and knowledge graphs.

**Covers.** Data architecture foundations and modeling; modern data architecture; data mesh (the four principles, data products, federated governance); data lakes and lakehouse (medallion zones, Iceberg/Delta/Hudi); big data (lambda and kappa); data engineering patterns (ingestion, CDC, idempotency, backfilling, orchestration); data observability and quality; knowledge graphs and GraphRAG; semantic web and semantic AI; the Microsoft data platform (Fabric, Power BI, Azure).

**Bundled knowledge.** `data-architecture-foundations.md`, `modern-data-architecture.md`, `data-mesh.md`, `data-lakes-and-lakehouse.md`, `big-data-architecture.md`, `data-engineering-patterns.md`, `data-observability-and-quality.md`, `knowledge-graphs.md`, `semantic-ai.md`, `semantic-web-and-linked-data.md`, `microsoft-fabric-and-powerbi-architecture.md`, `azure-data-fundamentals.md`, `software-engineering-for-data.md`.

**Trigger it with.** "design a data platform", "lakehouse vs warehouse", "data mesh", "build a data pipeline", "data quality", "knowledge graph", "ontology", "medallion architecture", "Microsoft Fabric".

**Example**

> **You:** Several teams want their own analytics but we have one central data team that is a bottleneck. Mesh or not?
>
> **Advisor:** Data mesh addresses exactly that organizational bottleneck, but it is an operating model, not just tech, so let me be honest about the cost. The four principles are domain ownership, data as a product, a self-serve platform, and federated computational governance. Mesh pays off when you have many domains, real platform capability, and leadership willing to push ownership outward; it hurts if you adopt the topology without the platform and governance, which just distributes the bottleneck. I will score your situation against the five placement parameters and, if mesh fits, give you the data-product contract template and the medallion layout for a first domain.

**Hands off to.** `devmentor-cloud-platform` for the infrastructure; `devmentor-ai-systems` when the data feeds RAG or models; `devmentor-distributed-systems` for streaming and consistency.

---

### 6.8 devmentor-cloud-platform - Cloud, Platform, and DevOps Architect

**Role.** Decides how software is packaged, deployed, scaled, operated, and secured in the cloud, and how to build the platform that lets teams do that well.

**Covers.** Cloud-native architecture and the twelve-factor app; containers and Docker; Kubernetes and orchestration; infrastructure as code and Terraform (and the OpenTofu split); multi-cloud; serverless infrastructure; the Well-Architected pillars; SRE (SLIs, SLOs, error budgets, toil, the golden signals); MLOps; platform engineering and internal developer platforms; cloud and container security.

**Bundled knowledge.** `cloud-native-architecture.md`, `containers-and-docker.md`, `kubernetes-and-orchestration.md`, `kubernetes-and-container-security.md`, `infrastructure-as-code-and-terraform.md`, `multi-cloud-architecture.md`, `serverless-architecture.md`, `cloud-solutions-architecture.md`, `azure-infrastructure-and-platform.md`, `site-reliability-engineering.md`, `mlops.md`, `systems-and-cli-application-design.md`.

**Trigger it with.** "containerize an app", "Kubernetes workloads", "write Terraform", "serverless vs containers", "set SLOs and error budgets", "platform engineering", "harden cluster security", "MLOps".

**Example**

> **You:** Should our new services run on Kubernetes or serverless functions?
>
> **Architect:** It depends on workload shape and team capacity, not fashion. Serverless wins for spiky, event-driven, or low-baseline workloads where you want zero idle cost and minimal ops, but watch cold starts, execution limits, and per-request cost at high steady volume. Kubernetes wins for steady high-throughput, long-running, or portability-sensitive workloads, but it is an operational commitment. For a small team I would default to serverless or a managed container runtime and only adopt Kubernetes when the workload or portability genuinely demands it. I will map your services to the right runtime and define the SLOs and error budgets so you operate them on evidence.

**Hands off to.** `devmentor-distributed-systems` for scale and resilience design; `devmentor-architecture-patterns` for service patterns; `devmentor-docs-delivery` for CI/CD and GitOps.

---

### 6.9 devmentor-ai-systems - AI and Agentic Systems Architect

**Role.** Designs AI-powered systems: LLM applications, RAG pipelines, and single- or multi-agent systems, with the protocols, evaluation, and security they need.

**Covers.** LLM and generative AI design patterns; RAG (chunking, embedding, retrieval, re-ranking, hybrid and graph RAG); agentic design patterns (reflection, tool use, planning, ReAct, orchestrator-worker, evaluator-optimizer) and multi-agent architecture topologies; the Model Context Protocol and A2A; agent platforms; context engineering and memory; LLMs in the enterprise; evaluation and guardrails; securing GenAI (prompt injection, the lethal trifecta, data exfiltration, OWASP for LLM and agentic AI); plus emerging tech (blockchain-AI convergence, digital twins).

**Bundled knowledge.** Under `knowledge/10-AI-GenAI-and-Agentic-Systems/`: `llm-design-patterns.md`, `generative-ai-design-patterns.md`, `building-genai-applications.md`, `agentic-design-patterns.md`, `agentic-architecture-patterns.md`, `multi-agent-systems.md`, `mcp-and-agent-protocols.md`, `agent-platforms.md`, `context-engineering` (in `llms-in-enterprise-and-context-engineering.md`), `securing-genai-systems.md`, `trustworthy-and-responsible-agents.md`, and the research frontier. Under `knowledge/16-Emerging-Technologies/`: blockchain and digital-twin files.

**Trigger it with.** "design an LLM app", "build a RAG pipeline", "architect an agent or multi-agent system", "which agent framework", "wire up MCP", "context or memory design", "evaluate or guardrail an AI system", "secure GenAI against prompt injection".

**Example**

> **You:** We want an internal assistant over our docs and tickets that can also take actions. How should we build it?
>
> **Architect:** Separate retrieval from action, because they have very different risk profiles. For retrieval, a RAG pipeline over docs and tickets: chunking tuned to your content, hybrid search (vector plus keyword), and re-ranking; add evaluation with a golden set and LLM-as-judge so you can measure regressions. For actions, an agent with explicit tools behind least privilege and human-in-the-loop on anything mutating. Critically, you are combining private data, tool use, and external content, which is the lethal trifecta for prompt injection, so I will design the guardrails (input and output controls, tool scoping, allowlists) and an evaluation and monitoring plan, and recommend MCP for the tool interface.

**Hands off to.** `devmentor-data-architecture` for the retrieval data; `devmentor-cloud-platform` to deploy and scale; `devmentor-ai-assisted-dev` if the question is about coding with AI rather than building an AI product.

---

### 6.10 devmentor-ai-assisted-dev - AI-Assisted Development Coach

**Role.** Helps you get more out of AI coding assistants while keeping engineering discipline intact. This is about how you build, not what you build.

**Covers.** Using GitHub Copilot, Cursor, and Claude Code well (completions, chat, agent modes, custom instructions); vibe coding practices and where they fit; GenAI-accelerated workflows across the SDLC; AI for cloud and ops automation; and the discipline that keeps AI-assisted work safe (verification, code review, security scanning, technical-debt and flow management, and the empirical findings on real productivity effects).

**Bundled knowledge.** `ai-coding-assistants-and-workflows.md`, `vibe-coding-practices.md`, `ai-assisted-engineering-discipline.md`, `ai-for-cloud-and-ops-automation.md`.

**Trigger it with.** "get more out of Copilot or Cursor or Claude Code", "set up agent modes", "vibe coding without losing control", "prompt structure for coding", "review and secure AI-generated code", "manage AI technical debt".

**Example**

> **You:** My team is shipping fast with AI but I am worried about quality. How do we keep control?
>
> **Coach:** Your instinct is right, and the evidence backs it: AI shifts the bottleneck from writing code to reviewing and verifying it, and AI-generated code carries materially higher defect and vulnerability rates if unguarded. Keep the speed, add three controls. First, treat AI output as a junior's pull request: it always gets human review and the same CI gates. Second, make tests and security scanning non-negotiable on AI-written paths. Third, use spec-driven prompting so the intent is explicit and reviewable rather than vibing. I will give you a lightweight checklist and a prompt structure that keeps you in control without killing the velocity.

**Hands off to.** `devmentor-quality-testing` for the verification strategy; `devmentor-ai-systems` if you are building an AI product rather than coding with AI; `devmentor-docs-delivery` for the review and CI gates.

---

### 6.11 devmentor-enterprise-architecture - Enterprise and Business Architecture Advisor

**Role.** Aligns technology with strategy across an organization, and plans the modernization of legacy estates without boiling the ocean.

**Covers.** EA frameworks (TOGAF and the ADM, Zachman, FEAF); business architecture and capability modeling, value streams; enterprise modeling and information systems; architecture modernization (strangler fig, branch-by-abstraction, socio-technical alignment, value-driven roadmaps); applied and agile enterprise architecture (making EA lean rather than heavyweight); EA maturity and governance.

**Bundled knowledge.** `enterprise-architecture-fundamentals.md`, `enterprise-architecture-practice.md`, `business-architecture.md`, `enterprise-modeling-and-information-systems.md`, `architecture-modernization.md`, `applied-enterprise-architecture.md`, `agile-enterprise.md`.

**Trigger it with.** "TOGAF", "Zachman", "business capability map", "value streams", "enterprise or domain architecture", "modernize a legacy estate", "run an EA practice", "align IT with strategy", "lean or agile EA".

**Example**

> **You:** We have a twenty-year-old monolith that everyone is afraid to touch, and leadership wants modernization. Where do we start?
>
> **Advisor:** Start from value and risk, not from a rewrite. A big-bang rewrite is the classic way to fail here. First, assess the current state and the drivers (what business outcome justifies the spend). Then identify seams using business capabilities and domain boundaries, and pick a first slice that is high value and reasonably isolated. Use the strangler fig: stand up a gateway, route the first capability to a new implementation, and migrate incrementally so you deliver value early and keep a working system throughout. Critically, align team boundaries to the new domains (socio-technical), or Conway's law will pull you back. I will produce a value-driven roadmap with the first three slices and the main risks.

**Hands off to.** `devmentor-architect` for the per-system design; `devmentor-architecture-patterns` for the decomposition patterns; `devmentor-product-discovery` to tie modernization slices to outcomes.

---

### 6.12 devmentor-quality-testing - Quality, Testing, and Verification Advisor

**Role.** Helps you choose the right mix of tests, design them to be maintainable, and decide when heavier verification is worth it.

**Covers.** Test strategy and the test pyramid; test levels (unit, integration, contract, system, end-to-end, exploratory); what to test and what not to; improving slow or flaky suites; test design patterns (page objects, test doubles, data builders, layered testing); meaningful coverage and test plans; formal methods and verification (specification, model checking, TLA+, property-based testing, design by contract) and when they pay off.

**Bundled knowledge.** `testing-strategy-and-practices.md`, `test-design-patterns.md`, `formal-methods-and-verification.md`.

**Trigger it with.** "define a test strategy", "balance the test pyramid", "add contract tests", "fix a flaky suite", "exploratory testing", "page objects or test doubles", "raise coverage meaningfully", "TLA+ or property-based testing", "design by contract".

**Example**

> **You:** Our end-to-end suite is slow and flaky and we still get bugs. What should we do?
>
> **Advisor:** The shape is wrong. A heavy, flaky end-to-end layer is the classic ice-cream-cone anti-pattern; it is slow, brittle, and gives false confidence. Rebalance toward the pyramid: push logic coverage down into fast unit tests, use contract tests at service boundaries so you can trust integrations without full end-to-end runs, and keep a thin layer of high-value end-to-end journeys. For flakiness, isolate the causes (timing, shared state, real networks) and apply test doubles and data builders. Tie the whole strategy to risk: test the parts where failure hurts most. I will give you a target test distribution and a plan to migrate without losing coverage.

**Hands off to.** `devmentor-requirements` to base tests on acceptance criteria; `devmentor-software-design` when testability needs design changes; `devmentor-ai-assisted-dev` for verifying AI-generated code.

---

### 6.13 devmentor-ia-ux - Information Architecture and UX Advisor

**Role.** Shapes how people find, understand, and act on information in your product, and connects that intent to the frontend that delivers it.

**Covers.** Information architecture (organization, labeling, navigation, and search systems; taxonomies and controlled vocabularies); IA practice (card sorting, tree testing, content inventory, sitemaps) and theory; enterprise and cross-channel IA; the UX process from research to design; usability heuristics; design psychology and cognition (how people perceive, read, remember, and decide); UX for AI-driven products (generative UI, handling uncertainty and trust); frontend architecture and UX engineering.

**Bundled knowledge.** `information-architecture.md`, `information-architecture-practice.md`, `information-architecture-theory.md`, `enterprise-information-architecture.md`, `ux-design-process-and-principles.md`, `design-psychology-and-cognition.md`, `ux-for-ai.md`, `frontend-architecture-and-ux-engineering.md`.

**Trigger it with.** "structure or restructure information", "design navigation or taxonomy", "card sorting or tree testing", "enterprise IA", "run a UX process", "usability heuristics", "cognitive load", "UX for AI features", "connect UX to frontend architecture".

**Example**

> **You:** Users cannot find anything in our app. The nav has grown to 14 top-level items.
>
> **Advisor:** Fourteen top-level items overwhelms working memory; people satisfice and miss things. Do not reorganize from the inside out. Run a content inventory, then an open card sort to learn the users' mental model, and validate the new structure with tree testing before you build it. You will likely collapse to five to seven groups with clearer labels (label by the user's task, not your org chart). I will give you the card-sort setup, candidate groupings, and the tree-test tasks to confirm findability, plus how the resulting IA maps to your frontend navigation components.

**Hands off to.** `devmentor-requirements` to turn flows into stories; `devmentor-product-discovery` to validate what matters; `devmentor-ai-systems` when the UX wraps an AI feature.

---

### 6.14 devmentor-docs-delivery - Documentation and Delivery Advisor

**Role.** Helps you capture architecture and knowledge clearly, and run dependable version control and delivery.

**Covers.** Documenting software architecture (Views and Beyond, C4, arc42, ADRs, documenting rationale); technical writing (the Diataxis model of tutorials, how-to, reference, and explanation; docs as code; READMEs and runbooks; writing docs that LLMs and agents can also consume); version control and delivery (branching strategies, pull requests and review, CI/CD with GitHub Actions, GitOps, trunk-based development).

**Bundled knowledge.** `documenting-software-architecture.md`, `technical-documentation.md`, `version-control-and-delivery.md`.

**Trigger it with.** "document an architecture", "write or organize an ADR", "apply C4 or arc42", "docs as code", "Diataxis", "write a README or runbook", "branching strategy", "set up CI/CD", "GitOps", "trunk-based development".

**Example**

> **You:** Our architecture lives in three people's heads and our docs are a mess. Where do we start?
>
> **Advisor:** Document for decisions and for newcomers, and keep it close to the code. Start with two cheap, high-leverage artifacts: a C4 context and container diagram so anyone can see the system at a glance, and an ADR log so the reasoning behind key choices stops evaporating. For the broader docs, use Diataxis to separate the four types people actually need (a tutorial, task-focused how-tos, dry reference, and conceptual explanation) instead of one undifferentiated wiki. Put it all in the repo as docs-as-code so it changes with the system. I will give you the C4 starter, an ADR template, and a docs structure.

**Hands off to.** `devmentor-architect` for the architecture content itself; `devmentor-cloud-platform` for the deployment pipelines behind delivery.

---

## 7. The commands

Commands are slash commands that produce a specific artifact from free-text arguments. They invoke the right specialist behind the scenes, so you do not have to know which one. All commands take everything after the command name as input.

### /devmentor-spec
Write a feature specification or PRD from a problem statement or rough idea.
- **Use for:** turning an idea into something buildable and testable.
- **Example:** `/devmentor-spec a saved-search feature that emails users when new matching listings appear`
- **You get:** problem and context, goals and non-goals, target users, user stories with acceptance criteria, functional and non-functional requirements, assumptions, open questions, and out-of-scope items.

### /devmentor-adr
Create an Architecture Decision Record for a decision and its alternatives.
- **Use for:** capturing a significant decision and its reasoning so it survives.
- **Example:** `/devmentor-adr adopt event sourcing for the orders service or keep CRUD`
- **You get:** title, status, context, the considered options with pros and cons, the decision, consequences, and whether it is a reversible (two-way) or irreversible (one-way) door.

### /devmentor-architecture-review
Review a proposed or existing architecture against quality attributes and trade-offs.
- **Use for:** a second opinion before committing, or a health check on what you have.
- **Example:** `/devmentor-architecture-review here is our current setup: a Rails monolith, a React SPA, Postgres, and three Lambda functions for image processing`
- **You get:** the key characteristics it should be judged on, an assessment of structure, style fit, scalability, resilience, data, operability, security, evolvability, and cost, plus the highest-leverage improvements.

### /devmentor-recommend-patterns
Recommend architecture, design, or integration patterns for a problem.
- **Use for:** finding the right pattern instead of guessing.
- **Example:** `/devmentor-recommend-patterns a user action must update inventory, billing, and shipping reliably`
- **You get:** a short list of candidate patterns, each with intent, why it fits, trade-offs, and what to watch for, plus patterns that would be a poor fit here and why.

### /devmentor-tradeoff-analysis
Analyze trade-offs between two or more options and give a reasoned recommendation.
- **Use for:** settling a genuine fork in the road.
- **Example:** `/devmentor-tradeoff-analysis Postgres vs DynamoDB for a multi-tenant SaaS catalog`
- **You get:** the decision drivers, a comparison across the characteristics that matter, the conditions under which each option wins, and a recommendation with its assumptions.

### /devmentor-system-design
Produce a system design for a given product or feature at a target scale.
- **Use for:** designing a system end to end, or preparing for a system design interview.
- **Example:** `/devmentor-system-design a rate limiter for an API gateway at 1M requests per second`
- **You get:** requirements and scope, back-of-the-envelope estimation, API and data model, building blocks, scaling and failure analysis, trade-offs, and a text architecture diagram.

### /devmentor-tech-selection
Evaluate and select a technology, framework, or vendor for a need.
- **Use for:** a defensible technology choice.
- **Example:** `/devmentor-tech-selection a message broker for an event-driven platform, mostly .NET, moderate volume`
- **You get:** weighted requirements, candidate comparison, lock-in and operational-burden analysis, current (2024-2026) context, and a recommendation with the conditions that would change it.

### /devmentor-test-strategy
Design a testing strategy for a system or change.
- **Use for:** deciding how to test something well rather than exhaustively.
- **Example:** `/devmentor-test-strategy a payments service integrating Stripe with retries and webhooks`
- **You get:** the test levels that matter here, what to test and what not to, test design patterns, data and environment needs, the automation approach, and where stronger verification is warranted.

### /devmentor-modernization-plan
Plan the modernization of a legacy system or estate.
- **Use for:** turning "modernize the monolith" into a sequenced, value-driven plan.
- **Example:** `/devmentor-modernization-plan a 15-year-old Java monolith that handles orders, inventory, and reporting`
- **You get:** current-state assessment, target and roadmap, a strategy (incremental vs big-bang, strangler fig, branch-by-abstraction), socio-technical alignment, an early-value sequence, and the main risks.

### /devmentor-threat-model-genai
Threat-model a GenAI or agentic system and recommend mitigations.
- **Use for:** securing an AI feature before it ships.
- **Example:** `/devmentor-threat-model-genai a customer-support agent with access to account data and refund tools`
- **You get:** a system map and trust boundaries, the threats (including prompt injection and the lethal trifecta, data exfiltration, excessive agency), likelihood and impact, layered mitigations, and relevant governance.

---

## 8. End-to-end worked scenarios

These show how to combine the agents across a real piece of work. You can drive each step yourself, or let `devmentor-hub` move you through them.

### Scenario A: greenfield product, from idea to first architecture

You have an idea for a B2B expense-management product and a small team.

1. **Discovery (`devmentor-product-discovery`).** "Help me figure out the smallest version of an expense tool worth building." The advisor builds an impact map, identifies the riskiest assumption (will finance teams trust automated categorization), and proposes a one-week test. You learn the core is approvals, not categorization.
2. **Requirements (`/devmentor-spec`).** `/devmentor-spec an expense approval flow where employees submit, managers approve, and finance exports to the accounting system`. You get stories with acceptance criteria and the non-functional requirements made explicit (auditability, approval latency).
3. **Architecture (`devmentor-architect`).** "Given these requirements and a team of five, what architecture style fits?" The architect ranks your characteristics (auditability, simplicity, time-to-market), and steers you to a modular monolith over microservices, capturing it with `/devmentor-adr`.
4. **Design (`devmentor-software-design`).** "Model the expense and approval domain." You get bounded contexts (Submission, Approval, Export) and the aggregates with their invariants.
5. **Patterns where needed (`devmentor-architecture-patterns`).** The accounting export is asynchronous and must be reliable, so you apply the transactional outbox without distributing the rest of the system.
6. **Testing (`/devmentor-test-strategy`).** A strategy weighted to fast unit tests around approval rules, contract tests at the accounting boundary, and a thin end-to-end happy path.
7. **Delivery and docs (`devmentor-docs-delivery`).** A C4 context and container diagram, an ADR log, trunk-based development, and a simple CI/CD pipeline.

The throughline: you build the right thing (discovery), specify it testably (requirements), shape it for your real constraints (architect), keep the code expressive (design), reach for patterns only where they earn their keep, and make it verifiable and documented.

### Scenario B: the system is falling over under load

Your service buckles during peaks and one slow dependency takes everything down.

1. **Triage (`devmentor-distributed-systems`).** You describe the symptoms. The engineer separates two problems: cascading failure across an integration point, and a scaling ceiling. For the first, you add timeouts, a circuit breaker, a bulkhead, and back-pressure.
2. **Scale (`devmentor-distributed-systems` + `/devmentor-system-design`).** You apply the AKF scale cube: clone stateless workers (X axis), split the hot write path into its own service (Y axis), and shard the largest table by tenant (Z axis), with the consistency implications spelled out.
3. **Run it (`devmentor-cloud-platform`).** You set SLOs and error budgets, the golden-signal dashboards, and autoscaling so you operate on evidence, not vibes.
4. **Lock it in (`/devmentor-adr` + `devmentor-docs-delivery`).** You record the resilience decisions and add a fitness function so the circuit-breaker and timeout policies do not silently regress.

### Scenario C: adding an AI assistant to an existing product

You want an assistant over your docs and tickets that can also take a few actions.

1. **Shape the AI design (`devmentor-ai-systems`).** Retrieval and action are separated. RAG over docs and tickets with hybrid search and re-ranking; an agent with narrowly scoped tools for actions.
2. **Feed it (`devmentor-data-architecture`).** The retrieval corpus is modeled, chunked, and kept fresh, with quality checks so the assistant does not answer from stale data.
3. **Secure it (`/devmentor-threat-model-genai`).** Because you combine private data, tools, and external content (the lethal trifecta), you add input and output guardrails, least-privilege tools, human-in-the-loop on mutations, and an evaluation and monitoring plan.
4. **Build it with discipline (`devmentor-ai-assisted-dev`).** Your team uses coding assistants to implement it, but every AI-written path gets review, tests, and security scanning.
5. **Design the experience (`devmentor-ia-ux`).** The assistant UX handles uncertainty and builds trust (showing sources, making actions confirmable), rather than pretending to be infallible.

### Scenario D: modernizing a legacy estate

Leadership wants the twenty-year-old monolith modernized.

1. **Plan (`/devmentor-modernization-plan`).** A value-driven roadmap: assess drivers, find seams by business capability, pick a high-value, well-isolated first slice, and use the strangler fig.
2. **Align teams (`devmentor-enterprise-architecture`).** Team boundaries are realigned to the target domains so Conway's law works for you, not against you.
3. **Design each slice (`devmentor-architect` + `devmentor-architecture-patterns`).** Each extracted capability gets a right-sized design and the decomposition patterns to peel it off safely (expose-then-extract, anti-corruption layer).
4. **Prove value early (`devmentor-product-discovery`).** Each slice is tied to a measurable outcome so modernization stays funded.

---

## 9. Best practices and tips

- **Begin with the hub when in doubt.** It is cheaper to be routed than to pick the wrong specialist and re-ask.
- **Front-load constraints.** Team size, scale, stack, deadline, compliance, and risk tolerance change the answer more than anything else.
- **Answer the clarifying questions.** They are the mechanism that turns a generic answer into a tailored one.
- **Ask for artifacts.** ADRs, specs, diagrams, test plans, and checklists are more useful than prose, and DevMentor prefers to produce them.
- **Interrogate the recommendation.** "When would the other option win?" and "what breaks this?" are first-class questions here.
- **Right-size everything.** DevMentor will steer a small team away from premature microservices, premature data mesh, or premature Kubernetes. Let it.
- **Chain deliberately, and loop back.** Discovery, requirements, architecture, design, patterns, testing, delivery is the natural order, but a downstream discovery often sends you upstream. That is normal.
- **Use the recency.** When current practice matters (AI, cloud, data, architecture trends), ask explicitly for the 2024-2026 view; the knowledge base carries it.

---

## 10. Quick reference

### Skill to concern

| If you need to... | Use |
|---|---|
| Decide what to build, scope an MVP, prioritize | `devmentor-product-discovery` |
| Write specs, stories, acceptance criteria | `devmentor-requirements` |
| Choose a style, weigh trade-offs, write an ADR | `devmentor-architect` |
| Go deep on microservices, events, APIs, integration | `devmentor-architecture-patterns` |
| Model a domain, pick design patterns, refactor | `devmentor-software-design` |
| Scale, add resilience, design a system, event sourcing | `devmentor-distributed-systems` |
| Build a data platform, mesh, lakehouse, pipelines | `devmentor-data-architecture` |
| Containers, Kubernetes, IaC, SRE, MLOps, platform | `devmentor-cloud-platform` |
| Build an LLM app, RAG, or agents; secure GenAI | `devmentor-ai-systems` |
| Use Copilot/Cursor/Claude Code well and safely | `devmentor-ai-assisted-dev` |
| Enterprise architecture, modernize a legacy estate | `devmentor-enterprise-architecture` |
| Define a test strategy or reach for formal methods | `devmentor-quality-testing` |
| Information architecture, UX, design psychology, UX for AI | `devmentor-ia-ux` |
| Document architecture, technical docs, Git and CI/CD | `devmentor-docs-delivery` |
| Anything broad or cross-cutting | `devmentor-hub` |

### Command to artifact

| Command | Produces |
|---|---|
| `/devmentor-spec` | Feature spec or PRD |
| `/devmentor-adr` | Architecture Decision Record |
| `/devmentor-architecture-review` | Architecture assessment |
| `/devmentor-recommend-patterns` | Pattern recommendations |
| `/devmentor-tradeoff-analysis` | Trade-off comparison and recommendation |
| `/devmentor-system-design` | End-to-end system design |
| `/devmentor-tech-selection` | Technology evaluation and choice |
| `/devmentor-test-strategy` | Test strategy |
| `/devmentor-modernization-plan` | Modernization roadmap |
| `/devmentor-threat-model-genai` | GenAI/agentic threat model |

### About the knowledge base

Every specialist carries its own `knowledge/` folder of synthesized topic files, distilled from 243 books and papers and enriched with research current to 2024-2026. Each file is structured the same way (Overview, Core concepts, Frameworks and models, Patterns, Principles and best practices, Anti-patterns and pitfalls, Decision guidance, Source attribution, Recent developments). The `devmentor-hub` additionally carries the consolidated pattern catalog, the quality-attributes catalog, and the source map. When DevMentor cites a framework or a source, it is drawing from this material, which is why its advice is both grounded and current.

---

*DevMentor v1.0.0. Conventions: American English, no em-dashes, trade-off honest, clarification first, artifact oriented.*


