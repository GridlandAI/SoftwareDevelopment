# Microservices in Polyglot Stacks (Python/FastAPI, Django, TypeScript/Node)

**Domain:** 05 - Architecture Patterns and Styles
**Primary sources:** Building Python Microservices with FastAPI (Tragura, 2022); Designing Microservices Using Django (Jain, 2020); TypeScript in Microservices Architecture (Baldurs, 2025)

## Overview

The microservices pattern catalog (decomposition, database-per-service, API gateway, saga, circuit breaker, and so on) is language-neutral, but the way you actually build a service is highly stack-specific. The framework you pick decides how you express endpoints, how you do dependency injection, how concurrency works, how you talk to a database, how you validate inputs, how you structure a project, and how you package and deploy. This file captures what differs across three popular implementation stacks and the building blocks each one gives you. It deliberately avoids re-documenting the core pattern catalog, which is covered elsewhere in this domain, and focuses on the practical question: given that I have decided on microservices, how do I build one well in this language?

The three stacks sit at different points on a spectrum. FastAPI is async-first and lightweight, designed around Python type hints and Pydantic validation. Django is batteries-included and synchronous by heritage, so "microservices with Django" is largely a story of decomposing the monolith-friendly framework and bolting on async workers, gateways, and deployment plumbing. TypeScript/Node is the most varied: you choose among Express, Fastify, and NestJS, and the headline benefit is end-to-end static typing of service contracts. Understanding these differences lets an agent give stack-appropriate advice rather than generic platitudes.

## Core concepts

- **Framework building blocks.** Every stack provides primitives for routing, request/response handling, middleware, and dependency wiring. FastAPI uses decorators on path operation functions plus `APIRouter` for grouping and `Depends()` for injection. Django uses apps, views, URL configs, and the Django REST Framework (DRF) for API serialization. Node frameworks range from Express's minimal middleware chain to NestJS's decorator-and-DI module system that mirrors Angular.
- **Async model.** This is the sharpest stack difference. FastAPI runs on ASGI/asyncio and is non-blocking by default, using coroutine switching rather than thread switching for I/O-bound work. Django was synchronous (WSGI) by origin and offloads async work to Celery task queues; ASGI support exists but the ecosystem still leans on workers. Node is single-threaded and event-loop-based, so async/await over Promises is the native concurrency model end to end.
- **Validation and contracts.** FastAPI derives validation, serialization, and OpenAPI docs automatically from Pydantic models and type hints. Django relies on DRF serializers. TypeScript gives compile-time contract checking via interfaces, plus runtime validation through libraries like Zod (paired with `z.infer` to keep the static type and runtime schema in sync), class-validator, or Fastify's JSON-schema validation.
- **Data access.** FastAPI commonly uses SQLAlchemy (sync or async) with a repository layer for relational data, and PyMongo/Motor with ODMs for MongoDB. Django ships its own ORM, which is tightly coupled to the framework and to a per-service database in a microservices split. TypeScript favors Prisma (type-safe client generated from the schema), TypeORM, or query builders.
- **Project structuring.** All three converge on layered structure (model -> repository -> service -> API) but express it differently. FastAPI leans on Depends-driven layering and optional DI containers. Django organizes by "apps." NestJS bakes layering into modules, providers, and controllers.

## Frameworks & models

### FastAPI building blocks (Tragura)
- **Path operations and routers.** Endpoints are functions decorated with `@app.get`, `@app.post`, and so on. `APIRouter` groups related endpoints and is included into the app or sub-application, which is the primary unit of project organization.
- **Dependency injection via `Depends()`.** FastAPI's DI is the backbone of clean structure: dependable functions or callable classes are injected into path operations, routers, or the app. They act as validators, filters, resource providers (DB sessions), and composition points. Dependencies can be nested and cached, and async dependencies are supported. Third-party containers (Dependency Injector, Lagom) add fuller IoC when needed.
- **Layered domain modeling.** The recommended structure separates a model layer (domain models), a repository layer (data access, often with repository factory methods), a service layer (business logic), and the REST API layer (path operations that call services). This keeps each layer independently testable.
- **Decomposition strategies.** Two decomposition approaches: by business unit (along organizational/structural lines, for stable processes) and by subdomain (along DDD domain models, for processes that evolve). The book builds a university ERP, decomposing into faculty, library, and student services.
- **Sub-applications and mounts.** FastAPI can mount independent sub-applications under a top-level app via `app.mount("/path", subapp)`. Each sub-app is a full FastAPI instance with its own routers and can also run standalone on its own port via Uvicorn. The top-level app can act as a pseudo-reverse-proxy gateway, an in-process API Gateway pattern.

### Django for microservices (Jain)
- **Decompose the batteries-included monolith.** Django's strength (a cohesive, all-in-one framework) is friction for microservices. The approach is to carve the monolith into services along business capabilities, each its own Django (or Django + DRF) project with its own database, communicating over HTTP/REST.
- **DRF for APIs.** The Django REST Framework provides serializers, viewsets, and authentication classes and is the standard way to expose Django data as service APIs. It also supplies JWT support via `djangorestframework_simplejwt`.
- **Async via Celery.** Because Django request handling is synchronous, background and near-real-time work (sending emails, generating reports, device monitoring, data maintenance) is pushed to Celery workers backed by RabbitMQ or Redis. This is the de facto async story for Django microservices.
- **Deployment stack.** The book is deployment-heavy: NGINX as reverse proxy, Gunicorn or uWSGI as the WSGI app server, PostgreSQL per service, Supervisor for process management, and Docker for packaging. It also covers AWS serverless (API Gateway + Lambda) as an alternative deployment target.

### TypeScript/Node framework selection (Baldurs)
- **Express.js** - minimal and flexible; middleware-chain architecture maps naturally onto cross-cutting concerns (auth, logging, validation). Good default from prototype to production when you want control.
- **Fastify** - performance-focused for high-throughput services; schema-based validation and serialization that align with TypeScript's type system, enforcing contracts at both compile time and runtime.
- **NestJS** - the enterprise-grade option; Angular-inspired modules, decorators, and built-in dependency injection. Structure scales well as service complexity grows, at the cost of more ceremony.
- **Type-safe contracts as the headline benefit.** TypeScript interfaces become compiler-enforced, always-synchronized service contracts. Structural typing lets each service declare exactly the subset of a shared entity it needs (an `OrderUser` with three fields rather than the full `User`), reducing coupling while preserving type safety across boundaries. Code generation (GraphQL Code Generator, OpenAPI generators) produces TypeScript types and clients from schemas, extending type safety across HTTP and GraphQL boundaries.

## Patterns

- **Sub-application mount as in-process gateway (FastAPI)** - Intent: compose several independently runnable FastAPI apps under one top-level gateway app. When to use: a system small enough that an out-of-process gateway is overkill, but you still want clear service boundaries and the option to split later. Trade-offs: simple and cheap, but the mounted apps share a process and host, so you lose true independent deployment and fault isolation until you extract them.
- **Repository + factory layering (FastAPI/Tragura)** - Intent: isolate data access behind a repository, created via factory methods, so the service layer is persistence-agnostic. When to use: any service with non-trivial data logic or more than one persistence backend. Trade-offs: more files and indirection for small CRUD services.
- **Celery task offload (Django/Jain)** - Intent: move non-immediate work out of the synchronous request path into queued workers. When to use: emails, reports, batch updates, anything tolerant of near-real-time latency. Trade-offs: adds a broker (RabbitMQ/Redis) and worker fleet to operate; introduces eventual completion semantics.
- **Schema-derived validation and docs (FastAPI Pydantic; Fastify JSON schema; Zod)** - Intent: define the data shape once and get validation, serialization, and documentation from it. When to use: every externally facing endpoint. Trade-offs: couples your API surface to the schema library; complex conditional validation can be awkward.
- **Structural-subset interfaces (TypeScript)** - Intent: each consumer service defines only the fields it needs from a shared entity, with the compiler verifying compatibility against the full type. When to use: shared domain entities consumed differently by multiple services. Trade-offs: requires a shared type definitions package and discipline to keep it authoritative.
- **Generated clients from contracts (TypeScript)** - Intent: generate typed clients/types from OpenAPI or GraphQL schemas so client and server stay in lockstep. When to use: stable, schema-first APIs. Trade-offs: a build/codegen step in the pipeline; drift if generation is skipped.

## Principles & best practices

- **Pick the framework to match the concurrency profile.** I/O-bound, high-fan-out services benefit from FastAPI's or Node's async-first models; CPU-bound work needs worker offload regardless of stack (Celery for Django/Python, worker threads or separate services for Node). FastAPI specifically degrades under heavy CPU-bound load because of thread switching, so use coroutine switching and offload CPU work.
- **Keep the API contract authoritative and machine-checked.** Use Pydantic/DRF serializers/Zod/JSON schema so the contract is enforced at runtime, and TypeScript interfaces or OpenAPI so it is enforced at build time. Living, compiler-enforced contracts beat prose documentation that rots.
- **Layer consistently.** model -> repository -> service -> API in all three stacks keeps business logic testable and swappable. Use the stack's native DI (FastAPI `Depends`, NestJS providers) rather than hand-wiring.
- **One database per service.** All three sources affirm database-per-service; do not let services share a schema, which reintroduces the coupling microservices exist to remove.
- **Containerize with multi-stage builds and non-root users.** The TypeScript book's Docker guidance (separate dependency/build/production stages, non-root user, health checks) generalizes to all three stacks.
- **Test along a pyramid adapted for distribution.** Heavy unit tests (cheap, fast, and in TypeScript catching type errors at compile time), fewer integration tests, and a thin layer of contract/end-to-end tests across service boundaries.

## Anti-patterns & pitfalls

- **Treating Django microservices like a distributed monolith.** Splitting code but keeping a shared database or coordinating releases across teams defeats independent deployability (the Jain book explicitly calls out the cross-team release-coordination tax).
- **Blocking the event loop / thread pool.** Running CPU-heavy or blocking calls inside async FastAPI path operations or the Node event loop causes contention and latency spikes. Offload to workers or run in a thread/process pool.
- **Over-mounting sub-applications and calling it microservices.** FastAPI mounts give boundaries but not isolation; shipping everything in one process is a modular monolith, not microservices.
- **Letting generated types or schemas drift.** Skipping codegen or editing generated clients by hand silently breaks the compile-time contract guarantee that is the whole point of the TypeScript approach.
- **Choosing NestJS for a trivial service.** Its structure pays off at scale but is overhead for a single small endpoint; Express or Fastify may be the better fit.
- **Equating "fewer lines of code" with microservices (Jain).** There is no line-count threshold; the boundary is about business capability and independent deployability, not size.

## Decision guidance

- **Python, async-first, type-hint-driven, new service:** FastAPI. Best when you want automatic validation/docs from Pydantic, native async, and lightweight footprint.
- **Existing Django shop or strong ORM/admin needs:** Django + DRF, with Celery for async and NGINX/Gunicorn or AWS Lambda for deployment. Accept that async is worker-based, not in-process.
- **TypeScript/Node:** choose Express for simplicity and control, Fastify for raw throughput plus schema validation, NestJS for enterprise structure and built-in DI. Reach for Prisma for type-safe data access and Zod for runtime validation that mirrors your static types.
- **Cross-stack contract strategy:** prefer schema-first (OpenAPI/GraphQL) with generated types when multiple teams or languages consume an API; prefer code-first (Pydantic/Zod/interfaces) when one team owns both sides and wants speed.
- **When CPU-bound:** none of these stacks make CPU work async for free; plan for worker queues (Celery) or dedicated compute services regardless of language.

## Source attribution

- FastAPI building blocks, dependency injection, decomposition by business unit vs subdomain, sub-application mounts as in-process gateway, repository/factory layering, coroutine switching and async model, security integrations (OAuth2, JWT, Keycloak, Okta, Auth0), and messaging (Celery, RabbitMQ, Kafka, SSE, WebSocket): Building Python Microservices with FastAPI (Tragura, 2022), Ch. 1-11.
- Django decomposition, DRF, JWT via simplejwt, Celery with RabbitMQ/Redis, NGINX/Gunicorn/uWSGI/Supervisor/Docker deployment, AWS serverless (API Gateway + Lambda), and the monolith-vs-microservices framing including the release-coordination cost: Designing Microservices Using Django (Jain, 2020), Ch. 8-15.
- TypeScript framework selection (Express/Fastify/NestJS), type-safe and structural-subset contracts, Prisma/Zod, codegen from OpenAPI/GraphQL, multi-stage Docker builds, and the microservices testing pyramid: TypeScript in Microservices Architecture (Baldurs, 2025), Ch. 1-15.

## Recent developments (2024–2026)

- **FastAPI cemented its position as the default Python service framework, with Pydantic v2 as the performance story.** Pydantic v2 (Rust-core `pydantic-core`) delivered large validation/serialization speedups that flow directly into FastAPI's request handling and OpenAPI generation, reinforcing the file's "schema-derived validation and docs" pattern. FastAPI remains async-first and type-hint-driven, and is now a standard choice for serving ML/LLM endpoints as well as CRUD services. (Pydantic v2 release notes; community benchmarks 2024-2025)
- **Type-safe, contract-first TypeScript stacks gained ground, with tRPC as a notable addition.** Beyond the Express/Fastify/NestJS choices in the file, tRPC rose for TypeScript-only estates wanting end-to-end type safety without GraphQL or codegen overhead; Zod-based runtime validation paired with static inference remained the dominant contract approach. This extends the file's "type-safe contracts as the headline benefit" thesis. (Java Code Geeks 2026; ruchitsuthar.com 2026)
- **MCP added a new cross-stack contract surface for AI agents.** With the Model Context Protocol standardizing in 2025-2026, FastAPI and Node services increasingly expose MCP server interfaces (typed tools, schema discovery, streaming) so agents can call them, sitting alongside REST/GraphQL contracts. Python and TypeScript MCP SDKs drove the bulk of adoption. (n1n.ai 2026; truthifi.com 2026)
- **The modular-monolith-first shift applies here too.** The broader 2025 "start with a modular monolith" consensus tempers the urge to split Python/Django/Node apps into many services prematurely; FastAPI sub-application mounts and Django apps are increasingly recommended as in-process module boundaries first, extracting services only when team scale demands it. (SoftwareSeni 2025; Java Code Geeks 2025)
- **Core stack-specific guidance is stable.** The async-model differences (FastAPI ASGI/asyncio, Django + Celery, Node event loop), database-per-service, layered structure, and multi-stage Docker builds remain the right advice; the 2024-2026 delta is faster validation tooling, tRPC, MCP contracts, and a more conservative decomposition posture.
