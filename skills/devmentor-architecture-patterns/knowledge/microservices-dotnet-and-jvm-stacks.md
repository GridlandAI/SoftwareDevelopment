# Microservices Reference Architectures: .NET and Spring/JVM Stacks

**Domain:** 05 - Architecture Patterns and Styles
**Primary sources:** .NET Microservices: Architecture for Containerized .NET Applications (de la Torre, Wagner & Rousos, 2022); Microservices with Spring Boot and Spring Cloud, 2nd Edition (Larsson, 2021); Hands-On Microservices with C# 8 and .NET Core 3 (Aroraa, Price & Hanselman, 2020); C# 9 Microservices Architecture Development with .NET 5 (Millie, 2024)

## Overview

The standard microservice patterns (decomposition, sagas, CQRS, event-driven integration) are catalogued in `microservices-patterns-catalog.md`, and their cross-stack realization (gateways, discovery, resilience, tracing, sidecars) in `microservices-implementation-patterns.md`. This file zooms out to the **whole-system reference architectures** the .NET and Spring/JVM ecosystems prescribe: what a complete production-ready microservice landscape looks like in each, which building blocks compose it, how those blocks map to the standard patterns, and how each ecosystem approaches the parts that are genuinely stack-specific - internal service layering (DDD/CQRS), testing strategy, and containerized deployment.

Two reference applications anchor the discussion. On .NET, Microsoft's **eShopOnContainers** (an e-commerce app of catalog, ordering, basket, and identity services) is the canonical specimen. On the JVM, Larsson's **product / recommendation / review / product-composite** landscape, built incrementally chapter by chapter, is the equivalent. Both teach the same architectural truths through different toolchains: database per service, eventual consistency via an event bus, API gateways as the single entry point, and a clear separation between logical architecture (the microservices and bounded contexts) and physical architecture (the containers and nodes they run on).

## Core concepts

- **Logical vs physical architecture.** A business microservice (a bounded context) is a *logical* boundary that may map to one or several physical containers/processes; the logical decomposition does not have to coincide one-to-one with the deployment topology (de la Torre).
- **The container is the unit of deployment.** In both stacks each service plus its dependencies and configuration is packaged as a container image, tested as a unit, and deployed independently - locally with Docker Compose, at scale on Kubernetes (de la Torre; Larsson; Millie).
- **Data sovereignty per service.** Each service owns its own database; cross-service consistency is achieved with application-level integration events over a logical event bus, embracing eventual consistency. In dev, databases may run as containers for fast onboarding; in production they should be managed database servers, not containers (de la Torre).
- **Two communication channels.** Synchronous HTTP (REST/gRPC) through API gateways for queries and command acceptance; asynchronous event-based messaging through an event bus for propagating updates across services (de la Torre; Larsson).
- **A reference architecture is a chassis writ large.** Each ecosystem's building blocks (Spring Boot starters + Spring Cloud; ASP.NET Core + Polly + health checks + Dapr/Aspire) are the concrete instantiation of the microservice chassis.

## Frameworks & models

### The .NET reference stack (de la Torre; Aroraa; Millie)

- **ASP.NET Core** as the service framework: minimal APIs or MVC/Web API controllers, built-in dependency injection (Millie emphasizes DI for loose coupling and testability), configuration, and the `Microsoft.Extensions.*` ecosystem.
- **Internal layering with DDD + CQRS.** de la Torre's ordering microservice models a rich domain: aggregates and aggregate roots, domain events to coordinate multiple aggregates, command handlers in the application layer that load an aggregate, invoke its behavior, and persist via a repository (the only transactional step). Domain logic stays inside the aggregate, fully encapsulated and unit-testable; an anemic command handler with leaked logic is a code smell. CQRS separates the write model (commands → domain) from read models (queries → thin data access). Aroraa and Millie likewise center DDD and bounded contexts.
- **Edge/API gateway:** Ocelot (lightweight, in-stack), YARP (reverse-proxy middleware), or Azure API Management (managed); BFF per client. Aroraa explicitly distinguishes API gateway (routing/aggregation) from API management (the broader product with metering, security, insights).
- **Communication & messaging:** HTTP via `IHttpClientFactory` (with Polly); async via an event bus abstraction over RabbitMQ or higher-level buses (Azure Service Bus, NServiceBus, MassTransit, Brighter). The transactional outbox keeps state changes and event publication atomic.
- **Resilience & health:** Polly policies (retry/jitter, circuit breaker, bulkhead, timeout, fallback) and `Microsoft.Extensions.Diagnostics.HealthChecks`.
- **Distributed runtime & observability:** Dapr sidecars for portable cross-cutting APIs; OpenTelemetry → Jaeger/Zipkin/Prometheus; Serilog + Seq; .NET Aspire for dev-time orchestration with a uniform `ServiceDefaults` chassis (Williams, cross-referenced).
- **Hosting:** Docker containers orchestrated by Kubernetes (AKS) or, in the older Aroraa text, Azure Service Fabric. Helm charts package deployments.

### The Spring/JVM reference stack (Larsson)

- **Spring Boot** as the service framework, with Spring WebFlux + Project Reactor for reactive non-blocking services, Spring Data (JPA/MongoDB) for persistence, springdoc-openapi for API description, and Spring Cloud Stream (over RabbitMQ/Kafka) for event-driven services.
- **Spring Cloud building blocks** map directly to the standard patterns: Netflix Eureka + Spring Cloud LoadBalancer (service discovery), Spring Cloud Gateway + Spring Security OAuth2/OIDC (edge server), Spring Cloud Config + Spring Cloud Bus (centralized configuration), Resilience4j (circuit breaker/retry/bulkhead/rate limiter/time limiter), Spring Cloud Sleuth + Zipkin (distributed tracing).
- **Platform & mesh layers** complete the picture: Kubernetes (Services, ConfigMaps/Secrets, Ingress, liveness/readiness probes, controller managers as the control loop) and Istio (ingress gateway, mTLS, Jaeger, Kiali, outlier detection) can *replace* several Spring Cloud components at runtime.
- **Observability stack:** EFK (Elasticsearch, Fluentd, Kibana) for centralized logging; Prometheus + Grafana for metrics; Zipkin/Jaeger for traces.
- **Hosting:** Docker + Kubernetes, deployed via Helm charts; native compilation (GraalVM) for fast-starting Java microservices.

### Pattern-to-building-block mapping (the takeaway model)

| Standard pattern | .NET building block | Spring/JVM building block | Platform/mesh option |
|---|---|---|---|
| Service framework | ASP.NET Core | Spring Boot | - |
| Service discovery | (platform-led) Kubernetes Service; or Steeltoe/Consul | Eureka + Spring Cloud LoadBalancer | Kubernetes Service; Istio |
| API gateway / edge | Ocelot, YARP, Azure APIM | Spring Cloud Gateway | Kubernetes Ingress; Istio ingress |
| Externalized config | appsettings + Azure App Config; ConfigMaps | Spring Cloud Config + Bus | Kubernetes ConfigMaps/Secrets |
| Resilience | Polly | Resilience4j | Mesh retries/outlier detection |
| Async messaging | MassTransit/NServiceBus over RabbitMQ/Service Bus | Spring Cloud Stream over RabbitMQ/Kafka | - |
| Distributed tracing | OpenTelemetry → Jaeger/Zipkin | Sleuth + Zipkin | Istio + Jaeger |
| Logging | Serilog + Seq | EFK | - |
| Metrics | OpenTelemetry → Prometheus | Micrometer → Prometheus + Grafana | Mesh telemetry |
| Health/control loop | HealthChecks + K8s probes | Actuator + K8s probes | Kubernetes controllers |
| Sidecar runtime | Dapr; .NET Aspire (dev-time) | (mesh sidecars) | Istio/Linkerd; Dapr |

## Patterns

- **Reference application as living architecture** - Intent: teach and validate an architecture through a complete, runnable system rather than fragments. When to use: as a template and proving ground (eShopOnContainers; Larsson's four-service landscape). Trade-offs: concrete and end-to-end; some choices (e.g., all SQL DBs in one container) are dev-only simplifications that must change for production. *(de la Torre; Larsson)*
- **Strangler-fig refactoring of a monolith** - Intent: incrementally carve a monolith into services, applying DDD to find boundaries, routing new functionality to services while the monolith shrinks. When to use: brownfield .NET estates. Trade-offs: continuous value and feedback vs the difficulty of decomposing tangled legacy code; Aroraa frames the whole book around this journey. *(Aroraa ch. 2)*
- **Rich domain model with command handlers (DDD/CQRS internals)** - Intent: keep business logic inside aggregates; command handlers orchestrate load→behave→persist; queries use separate read models. When to use: services with real business complexity (e.g., ordering). Trade-offs: testable, change-tolerant domains vs more ceremony; anemic handlers are an anti-pattern. *(de la Torre ch. 6; Millie ch. 3; Aroraa)*
- **Repository pattern for testability** - Intent: abstract persistence behind a repository so domain logic can be unit-tested in memory; testing against a real database is an integration test, not a unit test. When to use: any service with a domain model. Trade-offs: clean unit tests and swap-able persistence vs an extra abstraction; de la Torre warns that using `DbContext` directly forces mocking or in-memory databases. *(de la Torre ch. 6–7)*
- **Containerized, orchestrated deployment** - Intent: package each service as an image; run locally with Docker Compose; deploy at scale with Kubernetes (Helm charts) or Service Fabric. When to use: the default for both stacks. Trade-offs: independent scaling and deployment vs orchestration complexity. *(de la Torre ch. 4; Larsson ch. 15–17; Millie ch. 8; Aroraa)*

## Principles & best practices

- **Model boundaries with DDD before choosing technology.** Bounded contexts drive service boundaries in all four .NET/JVM texts; the gateway, discovery, and messaging choices follow.
- **Default to async event-based integration for updates**, reserving synchronous HTTP (through gateways) for queries and command acceptance; minimize synchronous chains (de la Torre; Larsson).
- **Own your data.** Database per service with eventual consistency via integration events; never let two services share logical tables (de la Torre; Larsson).
- **Build the landscape incrementally and keep it runnable.** Larsson adds one production-readiness concern per chapter and ships an automated `test-em-all` script that exercises the whole landscape after every change.
- **Keep the gateway thin or split it.** Avoid a single bloated aggregator; segregate by client (BFF) and business boundary (de la Torre; Aroraa).
- **Push concerns to the platform when it already provides them** (Kubernetes discovery/config/health; mesh mTLS/telemetry) rather than duplicating in app code (Larsson).
- **Promote through environments with the same artifacts:** build image → run unit + integration tests in an isolated container environment → deploy to AKS/Kubernetes for further integration testing (de la Torre; Millie ch. 12 automation).

## Testing & deployment in these ecosystems

### Testing strategy

- **The testing pyramid for microservices (Aroraa).** Many fast unit tests at the base; fewer component (single-service) tests; fewer still integration tests; consumer-driven **contract tests** to guarantee service compatibility without full integration; then performance and end-to-end (UI/functional) tests at the top. de la Torre's three-tier split mirrors this: unit tests, microservice functional/integration tests (involving that service's infrastructure), and application functional/integration tests (across services).
- **Unit tests** test a controller action or domain method in isolation, using fakes/mocks for dependencies; frameworks: xUnit.net, MSTest, NUnit, Moq (.NET); JUnit/Mockito (JVM). They are fast, run frequently, and do not catch infrastructure issues.
- **Integration tests** exercise real infrastructure (databases, message brokers, HTTP). ASP.NET Core provides a built-in test web host (`WebApplicationFactory`/`TestServer`) for in-process HTTP integration tests; on the JVM, Larsson uses **Testcontainers** to spin up real databases/brokers for persistence tests. These are orders of magnitude slower, so keep them few.
- **Contract testing** (consumer-driven) verifies that a provider still satisfies its consumers' expectations, catching breaking changes cheaply without standing up the whole system (Aroraa; Millie).
- **Chaos engineering / resilience testing.** Both modern .NET texts add fault injection: de la Torre tests HTTP retries and circuit breakers in eShopOnContainers; Larsson injects faults and delays (e.g., `faultPercentage`) to verify Resilience4j retries and circuit breakers; Millie covers chaos engineering for fault tolerance.
- **Landscape-level smoke tests.** Larsson's `test-em-all.bash` runs against the whole running landscape (verifying Eureka registration, gateway routing, security, and resilience behavior) and is rerun after every chapter's changes.

### Deployment

- **Local development:** Docker Compose brings up the full multi-container app (services + databases + broker) in one command; this is also the basis for integration-test environments (de la Torre; Larsson). .NET Aspire's `AppHost` plays this role with a live dashboard (Williams, cross-referenced).
- **Orchestrated production:** Kubernetes is the common target - Deployments, Services, Ingress, ConfigMaps/Secrets, liveness/readiness probes, and HPA autoscaling - packaged and released with **Helm charts** (Larsson ch. 16; Millie ch. 8). Larsson layers Istio on top for mesh capabilities and uses namespace labels (`istio-injection: enabled`) for automatic sidecar injection. Aroraa's older text targets Azure Service Fabric as well as AKS.
- **CI/CD automation:** build the container image once, run the test pyramid in isolated environments, then deploy the same image through environments; Millie devotes a chapter to automating microservice deployment and management. Native compilation (GraalVM) is offered by Larsson to shrink Java startup time for elastic scaling.
- **Production caveats:** managed databases (not containers) for HA/scale; environment-specific config and secrets via the config server or ConfigMaps/Key Vault; separate management/health ports to keep mesh metrics clean (de la Torre; Larsson).

## Anti-patterns & pitfalls

- **Anemic domain model / fat command handlers.** Business logic leaking out of aggregates into handlers or controllers defeats DDD and testability (de la Torre; Millie).
- **Testing against the database and calling it a unit test.** It is an integration test - slow and infrastructure-dependent; keep these few and fast unit tests many (de la Torre).
- **Too many slow integration/E2E tests.** They are orders of magnitude slower and brittle; rely on the pyramid and contract tests instead (Aroraa; de la Torre).
- **Databases as production containers.** Fine for dev onboarding, wrong for production HA/scale (de la Torre).
- **Single bloated API gateway.** Becomes a monolithic aggregator coupling all services; split by client and business boundary (de la Torre; Aroraa).
- **Big-bang monolith rewrite.** Prefer strangler-fig incremental extraction guided by DDD boundaries (Aroraa).
- **Synchronous HTTP chains across services.** Re-create a distributed monolith; prefer async integration events (de la Torre; Larsson).

## Decision guidance

- **Choosing a stack.** Both ecosystems cover every standard pattern; choose by team skills and platform. Spring Cloud gives the richest *library-level* coverage of discovery/config/resilience out of the box; .NET pairs ASP.NET Core with Polly, Dapr, and Aspire and leans on Kubernetes/managed Azure services for the platform concerns. If you already run Kubernetes + a mesh, much of Spring Cloud becomes optional (Larsson).
- **Where to put internal complexity.** Use DDD aggregates + command handlers + CQRS only where domain complexity justifies it (ordering, billing); keep simple CRUD services thin.
- **Designing the test suite.** Build a pyramid: many unit tests (in-memory domain via repositories), a moderate number of in-process integration tests (`WebApplicationFactory` / Testcontainers), consumer-driven contract tests at service seams, fault-injection tests for resilience policies, and a small set of landscape smoke and E2E tests.
- **Deployment path.** Docker Compose / Aspire for dev, the same images promoted via CI/CD into Kubernetes (Helm), mesh added only when fleet-wide mTLS/observability/traffic-shaping justify the control-plane cost; managed databases and externalized secrets in production.

## Source attribution

- **eShopOnContainers reference architecture, logical vs physical architecture, data sovereignty, two communication channels, DDD aggregates + command handlers + CQRS internals, repository pattern for testability, Ocelot/APIM gateways, IHttpClientFactory + Polly, health checks, three-tier testing (unit / microservice integration / application integration), WebApplicationFactory, retry/circuit-breaker fault testing, Docker + AKS + Helm deployment:** de la Torre, Wagner & Rousos, *.NET Microservices: Architecture for Containerized .NET Applications*, v6.0, 2022 (ch. 1, 3–8).
- **Spring Boot + Spring Cloud building-block stack, Eureka/Gateway/Config/Resilience4j/Sleuth-Zipkin mapping, reactive services, Testcontainers persistence tests, test-em-all landscape testing, Kubernetes + Helm + Istio deployment, native compilation:** Larsson, *Microservices with Spring Boot and Spring Cloud*, 2nd ed., 2021 (ch. 1–20).
- **Monolith-to-microservice refactoring journey, DDD for boundary finding, API gateway vs API management, full testing pyramid (unit/component/integration/contract/consumer-driven/performance/E2E), Azure Service Fabric:** Aroraa, Price & Hanselman, *Hands-On Microservices with C# 8 and .NET Core 3*, 3rd ed., 2020 (ch. 1–4).
- **.NET 5 service stack (minimal APIs, DI for testability), API gateway facade, DDD with microservices, Kubernetes deployment and management, contract testing, chaos engineering, deployment automation:** Millie, *C# 9 Microservices Architecture Development with .NET 5*, 2024 (ch. 1–13).

## Recent developments (2024–2026)

- **.NET Aspire became the headline change in the .NET reference stack.** Shipping with .NET 8/9 and iterating rapidly through 2024-2026, Aspire moved from a dev-time orchestration preview to a full cloud-native framework: a uniform `ServiceDefaults` chassis (`AddServiceDefaults()`) wiring OpenTelemetry, health checks, service discovery, and Polly resilience identically across services, plus a live dashboard. It is now widely described as "the Spring Boot of .NET." It also dropped the hard Docker Desktop dependency, supporting both Docker and Podman, with a YAML-free local-dev experience. (The New Stack, "Microsoft's .NET Aspire"; Microsoft Learn Aspire docs; DEV Community 2025)
- **Aspire + Dapr is now a first-class, documented integration.** The Aspire Dapr integration adds Dapr sidecars to Aspire resources and wires state-store, pub/sub, and component resources, letting teams get portable cross-cutting APIs without re-coding per service. This makes the file's "Dapr sidecars + Aspire dev-time orchestration" pairing a supported, mainstream path rather than a hand-rolled combination. (Microsoft Learn / Aspire Community Toolkit; Dapr .NET docs v1.15/v1.16)
- **eShopOnContainers was superseded by the modern "eShop" reference app.** Microsoft's canonical sample evolved toward a .NET 8/Aspire-based eShop, reflecting the shift away from the older Docker-Compose-centric eShopOnContainers; the architectural lessons (DDD aggregates, event bus, gateways, data-per-service) carry over but the reference implementation modernized. (Microsoft .NET architecture guidance, 2024-2026)
- **Service mesh is shifting to sidecar-less "ambient" mode, changing the mesh option in the mapping table.** Istio ambient mode (ztunnel + waypoint proxies) reached production maturity, offering mesh mTLS, telemetry, and routing without per-pod sidecars, and added ambient multicluster and Gateway API Inference Extension betas at KubeCon 2026. For both stacks this lowers the cost of pushing concerns to the mesh layer. (Cloud Native Now 2026; ambientmesh.io)
- **OpenTelemetry is now the default tracing path on both stacks.** Istio 1.20+ exports traces natively via OTLP (replacing Zipkin/Jaeger-specific formats), and Spring/.NET both standardized on OTel exporters; the file's "OpenTelemetry to Jaeger/Zipkin/Prometheus" guidance is now the universal default rather than one option. (istio.io; OpenTelemetry blog 2024)
- **Core stack guidance is stable; the movement is in the chassis and platform layers.** DDD aggregates, database-per-service, async event integration, the testing pyramid, Testcontainers, and Helm/Kubernetes deployment remain unchanged; the 2024-2026 delta is Aspire as a turnkey chassis, ambient mesh, and OTel-everywhere.
