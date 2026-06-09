# Applied Architecture in Practice

**Domain:** 04 - Software Architecture Fundamentals
**Primary sources:** Software Architecture with C# 12 and .NET 8 (Baptista & Abbruzzese, 2024); Scalable Software Architecture in C++ (Millie, 2024); Architectural Support for Software Performance in Continuous Software Engineering: A Systematic Mapping Study (Eramo, Tucci, Di Pompeo, Cortellessa, Di Marco & Taibi, 2024)

## Overview
This file is about putting architecture into real stacks: how layered and distributed structures are realized in production C# 12/.NET 8 and modern C++ codebases, how performance and scalability concerns surface during implementation, and how performance engineering is folded into continuous-integration pipelines. The two practitioner books are deliberately concrete - they tie architectural choices (non-functional requirements, microservice boundaries, smart-pointer ownership, concurrency models) to the code, tools, and cloud services that implement them - while the academic mapping study supplies the rigorous picture of how architectural models can support performance assessment inside DevOps/CI loops. The throughline is that architecture is not finished at the diagram: layering, scaling decisions, memory and concurrency design, testing, and CI/CD all carry architectural weight, and performance must be engineered continuously rather than checked once.

This matters across the SDLC because the gap between a clean architecture and a fast, scalable, maintainable running system is closed in implementation and operations. Decisions about how to scale (up vs. out), how to manage resources, how to structure CI/CD stages, and where to measure performance are architectural decisions made in code and pipelines.

## Core concepts

- **Non-functional requirements (NFRs).** Aspects that do not change *what* the software does but determine whether it is good software: scalability, availability, resiliency, performance, multithreading, interoperability, usability, and security. Baptista & Abbruzzese treat eliciting and meeting NFRs as the core architectural task, distinct from functional requirements. (Baptista & Abbruzzese, 2024)
- **Scalability is software, not just hardware.** A sustainable scalability model comes from overall software design; the fewer resources spent per process, the more users the system handles. Treating scalability as "add more hardware" ignores running cost, which is itself an architectural factor. (Baptista & Abbruzzese, 2024; Millie, 2024)
- **Vertical scaling (scale up) vs. horizontal scaling (scale out).** Scale up = more powerful hardware for one instance; scale out = more instances behind a load balancer, with auto-scale rules (e.g., triggered by CPU). Scale out adapts automatically to variable load and underpins availability. (Baptista & Abbruzzese, 2024)
- **Resiliency.** The ability to recover from failure without exposing it to the end user; essential in cloud scenarios because of transient errors, updates, and resets across multiple systems. (Baptista & Abbruzzese, 2024)
- **High availability.** Achieved by combining scalability and resiliency (e.g., zone redundancy, horizontal scaling), sometimes requiring purpose-built designs. (Baptista & Abbruzzese, 2024)
- **Separation of concerns in code.** Millie's first core principle: isolate functionality into distinct modules - business logic from presentation, platform-specific code from core logic, data access encapsulated - for maintainability, reuse, and testability. (Millie, 2024)
- **Resource ownership and memory model (C++).** Smart pointers and move semantics make ownership explicit and transfers efficient, which is architecturally significant in C++ because it determines lifetime, safety, and performance at scale. (Millie, 2024)
- **Concurrency and parallelism.** First-class architectural concerns for scalable systems: threads, futures/promises for asynchronous deferred results, and the async/await pattern in C# to avoid blocking and performance bottlenecks. (Millie, 2024; Baptista & Abbruzzese, 2024)
- **Continuous Software Engineering (CSE).** A process that interleaves business strategy (requirements), development, and operations on a continuum; its development-phase activities are continuous integration, deployment/release, delivery, and verification/testing, while the operation phase emphasizes continuous monitoring and use. DevOps is the standout practice; BizDev extends it to business management. (Eramo et al., 2024)
- **Software performance (SP) as a quality aspect.** The whole collection of activities aimed at meeting performance requirements (response time, throughput, resource utilization) and the trade-offs between performance and other QoS attributes (security, reliability, availability). (Eramo et al., 2024)
- **Architecture-as-performance-model.** Software architectures can be transformed into performance models whose indices let architects compare architectural alternatives; evolving architectural models can feed runtime data back to support performance decisions during CSE. (Eramo et al., 2024)

## Frameworks & models

### NFR-driven architecture in .NET 8 / Azure (Baptista & Abbruzzese)
The book organizes applied architecture around delivering NFRs on a concrete stack:
- **Scalability/availability/resiliency** via Azure App Service (scale up by changing the hosting tier; scale out via instance count or auto-scale rules; zone redundancy for availability) and ASP.NET Core on .NET 8 (cross-platform, high performance, microservice-ready, LTS). Deployment can be framework-dependent or self-contained, via publish profiles or continuous deployment from a repository.
- **Performance** through C# practices (async/await to avoid blocking), code metrics measured in Visual Studio, and code inspection against a standard.
- **Microservices with .NET** as the evolution of "modules": independently deployable services communicating via gRPC and RabbitMQ; frontend microservices implemented with ASP.NET Core; orchestrated on Kubernetes.
- A running **case study (WWTravelClub)** threads these decisions through Azure, Azure DevOps, GitHub, C# 12, .NET 8, and Kubernetes. (Baptista & Abbruzzese, 2024)

### DevOps principles and CI/CD (Baptista & Abbruzzese)
DevOps is "the union of people, process, and products to enable continuous delivery of value." Its three enabling principles:
- **Continuous Integration (CI).** Every commit triggers automatic build plus unit and functional tests, keeping the main branch stable and surfacing problems fast; encourages frequent merges to minimize conflicts.
- **Continuous Delivery (CD).** A multi-stage release pipeline (Development/Tests → Staging → Production) so production stays safe; recommends pre-deployment approvals and change management (ITIL/ISO 20000) rather than reckless continuous production deployment.
- **Continuous Feedback.** Production telemetry (e.g., Application Insights, Test & Feedback) flows back to improve infrastructure, code, and UI.
The book explicitly warns that DevOps does not require CI/CD, and that direct continuous deployment to a 24/7 production system is hard, expensive, and risky without mature processes. (Baptista & Abbruzzese, 2024)

### Scalable C++ structure (Millie)
A practitioner progression for building C++ systems that last:
- **Core principles** (separation of concerns, decoupling for reuse, encapsulating data access) and OOP-for-scalability.
- **Design patterns** applied in C++ (Singleton, Adapter, Observer, and the creational/structural/behavioral families) as reusable solutions for scalable systems.
- **Memory and performance machinery**: smart pointers, move semantics, and the STL for scalable collections.
- **Concurrency**: threads, parallelism, futures/promises for asynchronous work.
- **Distributed systems**: building applications across multiple machines.
- **Testing + CI/CD**: unit testing (Google Test/Catch2), mocking (Google Mock), the Arrange-Act-Assert pattern, and integrating static analysis (clang-tidy), build/test, staging deployment, and Terraform-provisioned infrastructure into a GitHub Actions pipeline.
- **Maintainability**: code reviews and best practices (including Git workflow). A worked case study designs a scalable C++ application end to end. (Millie, 2024)

### Performance-targeted CSE: a classification framework (Eramo et al.)
A systematic mapping study (215 candidate papers → 66 primary studies) that produces:
- **A reusable classification framework** for comparing/evaluating solutions, methods, and techniques specific to architectural support for software performance in CSE.
- **A systematic map** of the research state across four research questions: (RQ1) which research areas and target systems have been investigated; (RQ2) what performance problems are addressed and how; (RQ3) which instruments/tools are adopted; (RQ4) the current research gaps and their implications for future research.
- **Trends and gaps**: architectural models are gaining relevance for supporting performance decisions in CSE, but current CSE/DevOps practice still prioritizes rapid delivery over systematic per-change performance assessment, and assessing performance on every change is "not standard practice yet." Efficient, automated performance-engineering tooling integrated into the continuous pipeline is identified as a critical need. (Eramo et al., 2024)

## Patterns
Applied/implementation patterns emphasized across these sources:

- **Layered separation (presentation / business logic / data access)** - *Intent:* isolate concerns so each layer changes independently (e.g., swapping SQL for MongoDB touches only data access). *When to use:* almost any application as a default structure. *Trade-offs:* maintainability and testability vs. potential pass-through overhead. (Millie, 2024; Baptista & Abbruzzese, 2024)
- **Singleton** - *Intent:* ensure a single shared instance of a class. *When to use:* a single point of coordination/configuration. *Trade-offs:* convenient global access vs. hidden coupling and testability problems if overused. (Millie, 2024)
- **Adapter** - *Intent:* make incompatible interfaces work together. *When to use:* integrating third-party or legacy components with a different contract. *Trade-offs:* decoupling and reuse vs. an extra indirection layer. (Millie, 2024)
- **Observer** - *Intent:* notify dependent objects of state changes. *When to use:* event/notification fan-out, reactive updates. *Trade-offs:* loose coupling vs. harder-to-trace control flow. (Millie, 2024)
- **Microservices (with gRPC + message broker)** - *Intent:* independently deployable services communicating over efficient RPC (gRPC) and asynchronous messaging (RabbitMQ). *When to use:* enterprise systems needing independent scale/deploy and team autonomy, with Kubernetes for orchestration. *Trade-offs:* agility and scalability vs. distributed-systems and operational complexity. (Baptista & Abbruzzese, 2024)
- **Multi-stage CD pipeline (Dev/Test → Staging → Production)** - *Intent:* protect production by promoting builds through environments with approvals. *When to use:* any system requiring stable production, especially 24/7. *Trade-offs:* safety and quality vs. pipeline cost and slower path to production. (Baptista & Abbruzzese, 2024)
- **RAII / smart-pointer ownership (C++)** - *Intent:* bind resource lifetime to object scope and express ownership explicitly via smart pointers, transferring efficiently with move semantics. *When to use:* any non-trivial C++ system where lifetime and performance matter. *Trade-offs:* safety and performance vs. requiring disciplined ownership design. (Millie, 2024)
- **Architecture-to-performance-model transformation** - *Intent:* derive performance models from architectural models to compare alternatives and feed runtime data back into design. *When to use:* performance-critical systems under continuous evolution. *Trade-offs:* early, model-based performance insight vs. modeling effort and limited tooling maturity. (Eramo et al., 2024)

## Principles & best practices

- **Design for sustainable scalability, not just more hardware.** Lower per-process resource use scales further and saves running cost; treat running cost as an architectural factor. (Baptista & Abbruzzese, 2024)
- **Prefer scale-out with auto-scale rules for variable load**; use scale-up for predictable headroom; combine with redundancy for availability. (Baptista & Abbruzzese, 2024)
- **Build for failure (resiliency).** Recover from transient cloud failures without exposing them to users. (Baptista & Abbruzzese, 2024)
- **Use async/await and proper concurrency to avoid blocking bottlenecks.** Asynchrony is a performance/responsiveness lever, not an afterthought. (Baptista & Abbruzzese, 2024; Millie, 2024)
- **Make resource ownership explicit in C++** with smart pointers and move semantics; lean on the STL for scalable collections. (Millie, 2024)
- **Automate quality in the pipeline.** Run static analysis (clang-tidy), unit/functional tests, and code metrics in CI; promote through staging with approvals before production. (Millie, 2024; Baptista & Abbruzzese, 2024)
- **Structure tests with Arrange-Act-Assert and mock external dependencies** to isolate the unit under test. (Millie, 2024)
- **Treat CI, CD, and continuous feedback as a loop.** Telemetry from production should feed the next development cycle. (Baptista & Abbruzzese, 2024)
- **Engineer performance continuously, ideally model-based.** Use evolving architectural models to assess performance across changes rather than testing once; invest in automated performance tooling in the pipeline. (Eramo et al., 2024)
- **Security and interoperability by design.** Achieve security by design and target cross-platform interoperability (.NET 8 runs on Windows/Linux/macOS/containers). (Baptista & Abbruzzese, 2024)

## Anti-patterns & pitfalls

- **Equating scalability with hardware.** Leads to runaway running costs and ignores the software-design root cause. (Baptista & Abbruzzese, 2024)
- **Reckless continuous production deployment.** Pushing every commit straight to a 24/7 production system without mature processes, multi-stage pipelines, approvals, and change management is dangerous and expensive. (Baptista & Abbruzzese, 2024)
- **Assuming DevOps demos reflect reality.** The "fast track to deploy" demos hide the sophistication and team maturity real CI/CD requires; database scripts and environment config outside the pipeline can damage production. (Baptista & Abbruzzese, 2024)
- **Over-using Singleton / global state.** Introduces hidden coupling and undermines testability. (Millie, 2024)
- **Sloppy ownership in C++.** Unclear lifetimes and unnecessary copies cause leaks, crashes, and performance loss at scale; avoid via smart pointers and move semantics. (Millie, 2024)
- **Treating performance as a one-time gate.** Per-change performance assessment is not yet standard practice; relying on a single late check lets degradations (latency, throughput, resource use) slip into releases. (Eramo et al., 2024)
- **Design-time-only performance analysis.** Early comparative analyses help but do not extensively account for target-platform characteristics; pair them with runtime data in the continuous loop. (Eramo et al., 2024)

## Decision guidance
How an agent should reason about applied/implementation architecture:

1. **Start from the NFRs and quantify them.** Identify scalability, availability, resiliency, performance, security, and interoperability targets before choosing infrastructure; let those drive the scaling and deployment strategy. (Baptista & Abbruzzese, 2024)
2. **Choose a scaling strategy by load shape and cost.** Variable/unpredictable load → scale out with auto-scale rules plus redundancy. Steady load needing more headroom → scale up. Always weigh running cost. (Baptista & Abbruzzese, 2024)
3. **Decide monolith vs. microservices on operational maturity.** Microservices (gRPC + broker + Kubernetes) pay off only when the team/org can absorb distributed-systems complexity; otherwise keep modules in a well-separated layered/monolithic design. (Baptista & Abbruzzese, 2024; Millie, 2024)
4. **In C++, design ownership, concurrency, and collection use deliberately.** Use smart pointers/move semantics for lifetime and performance, choose the right concurrency primitive (threads vs. futures/promises), and lean on the STL. (Millie, 2024)
5. **Bake quality and performance into the pipeline.** Static analysis + unit/functional tests + code metrics in CI; multi-stage CD with approvals; production telemetry feeding back. Add model-based, automated performance assessment so regressions are caught per change, not at the end. (Millie, 2024; Baptista & Abbruzzese, 2024; Eramo et al., 2024)
6. **When performance is critical and the system evolves continuously, transform architecture into performance models** to compare alternatives and to interpret runtime data, accepting current tooling-maturity limits. (Eramo et al., 2024)

## Source attribution

- **Baptista & Abbruzzese (2024), *Software Architecture with C# 12 and .NET 8.*** Non-functional requirements, scalability (up/out) and resiliency/availability on Azure + ASP.NET Core/.NET 8, async/await and code metrics, microservices with .NET (gRPC, RabbitMQ, frontend microservices, Kubernetes), DevOps principles (CI, CD, continuous feedback), multi-stage release pipeline and CI/CD risks, security/interoperability by design, and the WWTravelClub case study. Informs Overview, Core concepts (NFRs, scaling, resiliency, CSE/DevOps angle), Frameworks & models (NFR-driven .NET architecture, DevOps/CI-CD), Patterns (microservices, multi-stage CD, layering), and the scaling/deployment principles, anti-patterns, and guidance.
- **Millie (2024), *Scalable Software Architecture in C++.*** Core principles (separation of concerns, decoupling, data-access encapsulation), OOP for scalability, design patterns in C++ (Singleton, Adapter, Observer), smart pointers and move semantics, the STL, concurrency/parallelism and futures/promises, distributed systems, testing for scalability (Google Test/Catch2, Google Mock, Arrange-Act-Assert) with CI/CD (GitHub Actions, clang-tidy, staging deploy, Terraform), and maintainability/code reviews. Informs Overview, Core concepts (separation of concerns, C++ memory/concurrency), Frameworks & models (scalable C++ structure), Patterns (Singleton/Adapter/Observer, RAII/ownership, layering), and the C++ and CI/testing principles, anti-patterns, and guidance.
- **Eramo, Tucci, Di Pompeo, Cortellessa, Di Marco & Taibi (2024), *Architectural Support for Software Performance in Continuous Software Engineering: A Systematic Mapping Study.*** The CSE/DevOps/BizDev framing, software performance as a quality aspect, architecture-as-performance-model, the reusable classification framework, the systematic map and four research questions (areas/target systems, problems, instruments, gaps/implications), and the finding that per-change performance assessment is not yet standard and automated pipeline tooling is needed. Informs Overview, Core concepts (CSE, SP, architecture-to-performance-model), Frameworks & models (performance-targeted CSE framework), Patterns (architecture-to-performance-model transformation), and the continuous-performance principles, anti-patterns, and guidance.

## Recent developments (2024–2026)

- **.NET advanced fast (the practitioner stack moved past .NET 8).** .NET 9 (STS) and then .NET 10 (LTS, released November 2025) extended the cloud-native and performance themes of Baptista & Abbruzzese's .NET 8 book: .NET 10 emphasizes Native AOT (smaller size, faster startup), JIT and hardware-acceleration improvements, an "AI-ready" runtime, and cloud-native features including native Kubernetes operators and auto-healing. Published response-time and resource benchmarks claim large gains over .NET 9. Architects targeting the same NFRs (scalability, performance, resiliency) now have stronger AOT and Kubernetes-native primitives to work with. (InfoQ, ".NET 10 Officially Released," 2025, https://www.infoq.com/news/2025/11/dotnet-10-release/)

- **Continuous performance testing matured into pipeline practice.** The Eramo et al. gap ("per-change performance assessment is not yet standard") is being closed by developer-first load-testing tools (k6, Gatling, JMeter, NeoLoad) that integrate directly into GitHub Actions, GitLab CI, Azure DevOps, and Jenkins, making per-change performance checks more routine for cloud-native and microservice systems. (Cloud-native performance testing tools roundup, 2025, https://www.allconsultingfirms.com/blog/cloud-native-performance-testing-tools-2025/)

- **Cell-based architecture for resilience at the implementation level.** AWS Well-Architected guidance on cell-based architecture (each cell a complete, independently operable workload instance) gives applied teams a concrete pattern to bound blast radius beyond zones/regions, complementing the scale-out/redundancy NFR techniques in this file. (AWS Well-Architected, "Reducing the Scope of Impact with Cell-Based Architecture," 2024-2025, https://docs.aws.amazon.com/wellarchitected/latest/reducing-scope-of-impact-with-cell-based-architecture/reducing-scope-of-impact-with-cell-based-architecture.html)

- **Sustainability and FinOps as implementation-level NFRs.** The Green Software Foundation's SCI (now ISO/IEC 21031:2024, extended to SCI for AI in late 2025) plus FinOps practice push energy efficiency and running cost from abstract concerns into measurable, pipeline-checkable targets, reinforcing this file's "scalability is software, treat running cost as architectural" stance. (Green Software Foundation SCI, https://sci.greensoftware.foundation/)

- **AI copilots entering CI/CD.** Pipelines increasingly include AI assistants that comment on PRs, detect anomalies, summarize logs, and propose remediations, adding an AI layer to the CI/CD and continuous-feedback loop these sources describe. (Cloud Native Now / platform-engineering trend coverage, 2025)

- **Practitioner fundamentals stable.** Layering, scale-up vs scale-out, resiliency/availability, RAII/ownership in C++, multi-stage CD, and architecture-as-performance-model remain valid; the 2024-2026 development is newer runtimes (.NET 10 / AOT), pipeline-integrated performance testing, cell-based resilience, and sustainability/cost as measurable targets.
