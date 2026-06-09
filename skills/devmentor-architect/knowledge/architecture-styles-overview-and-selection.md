# Architecture Styles: Overview and Selection

**Domain:** 04 - Software Architecture Fundamentals
**Primary sources:** Fundamentals of Software Architecture (Richards & Ford, 2020); Head First Software Architecture (Gandhi, Richards & Ford, 2023); Software Architecture for Developers (Abrams, 2024); Software Architecture: The Hard Parts (Ford et al., 2021)

## Overview
An architecture style is the overarching structure of a system: how the user interface and backend code are organized (in layers of a monolith, or as separately deployed services) and how that code interacts with data. Style names are shorthand: "layered monolith" or "microservices" instantly conveys a topology, a typical deployment model, a data strategy, and a well-known set of characteristic strengths and weaknesses. Architects must know the common styles and their built-in trade-offs because each embodies a fixed bargain that suits some problems and fights others.

This note is a selection-level overview. It summarizes the eight main styles Richards and Ford catalog, their characteristic strengths and weaknesses, and the method for choosing among them. Deep per-style detail (full topologies, internal patterns, variants) lives in domain 05 (Architecture Patterns and Styles); this note stays at the level needed to choose well.

## Core concepts

**Style vs pattern.** A style is the overall structure (layered, microservices). A pattern is a lower-level design structure used to solve specific problems within a style. Choosing a style is the most strategic, hardest-to-change decision an architect makes, so getting it approximately right early matters (Head First's house metaphor: you cannot switch a one-story ranch to a three-story Victorian mid-construction).

**Monolithic vs distributed.** Monolithic styles deploy as a single unit, usually against one database (layered, modular monolith, microkernel, pipeline). Distributed styles run as multiple services communicating over the network (service-based, event-driven, microservices, space-based). The fundamental selection question is how many architecture quanta the design needs: one set of characteristics favors a monolith; differing characteristics across parts force distribution.

**Star ratings (Richards & Ford).** Each style chapter rates characteristics from one star (poorly supported) to five (a key strength). The ratings are the quickest way to match a style to the prioritized characteristics from the requirements.

## Frameworks & models

**The eight styles and their selection profiles (Richards & Ford).**

- **Layered (n-tier) monolith.** Technically partitioned into horizontal layers (presentation, business, persistence, database) with layers of isolation (closed layers control change). Strengths: simplicity, familiarity, low cost; the natural default and good starting point when the right style is still unknown. Weaknesses: low deployability, low testability, poor scalability/elasticity, no fault tolerance (a single quantum); ratings degrade as the monolith grows. Use for small, simple apps and tight budgets.

- **Modular monolith.** Single deployment partitioned around domains with one database. Strengths: business-aligned structure, simpler than distributed styles, clean migration path to microservices. Weaknesses: shares a monolith's deployment/scaling limits; needs governance to keep module boundaries from eroding. A strong default for new systems.

- **Pipeline (pipes and filters).** Unidirectional pipes connect single-purpose filters. Strengths: simplicity, extensibility, low cost, good for transformation/ETL and EDA-style processing. Weaknesses: monolithic deployment limits scalability/elasticity/fault tolerance. Use for data-transformation workflows.

- **Microkernel (plug-in).** A core system plus plug-in components. Strengths: customizability, extensibility, adaptability, isolation of features; a natural fit for product-based applications and any domain that needs pluggable behavior (domain/architecture isomorphism). Weaknesses: monolithic core limits scalability; contracts to plug-ins must be governed.

- **Service-based.** A hybrid/distributed style: an application split into a handful of coarse-grained, separately deployed domain services, typically sharing a database. Strengths: good agility, deployability, testability, and fault tolerance at much lower complexity than microservices; no database split or operational automation required. Weaknesses: less granular scalability than microservices. An excellent stepping-stone from monolith to microservices (Hard Parts).

- **Event-driven.** Asynchronous, decoupled components communicating via events (mediator/orchestrated or broker/choreographed). Strengths: high performance, scalability, elasticity, fault tolerance, responsiveness. Weaknesses: harder to test, reason about, and debug; data consistency and error handling are complex. Use for highly scalable, reactive, asynchronous workloads.

- **Microservices.** Domain-partitioned, fine-grained services each owning its data (bounded contexts), independently deployable. Strengths: best-in-class agility, scalability, elasticity, deployability, testability, fault tolerance, and differing characteristics per service. Weaknesses: high overall complexity, performance challenges from network/orchestration, and distributed-data difficulty; relies on mature DevOps. Use when different parts genuinely need different characteristics and the organization can support the operational burden.

- **Space-based.** Built around replicated in-memory data grids to remove the database bottleneck. Strengths: extreme scalability and elasticity, high performance under unpredictable spikes. Weaknesses: complexity and cost; harder to test; eventual consistency in the backing store. Use for very high, variable concurrent load (the "lose Italy" / ticket-sale class of problem).

**The four dimensions context (Head First).** A style is only one of four interdependent dimensions (characteristics, decisions, logical components, style). You cannot pick a style without first knowing the prioritized characteristics; the style must align with the characteristics and the decisions.

**Decision criteria for choosing a style (Richards & Ford, Chapter 18).** The architect should be comfortable with: the domain; the characteristics that affect structure; the data architecture; organizational factors (cost, M&A plans); process/team/operational maturity; and domain/architecture isomorphism (does the problem's shape match the style's shape - microkernel for customizable products, space-based for many discrete operations). Then make three determinations:

1. **Monolith vs distributed** - count the quanta (one set of characteristics vs many).
2. **Where should data live** - one database for a monolith; per-service ownership and data flow for distributed.
3. **Communication style** - synchronous by default, asynchronous when necessary.

The output is a topology, architecture decision records for the hardest choices, and fitness functions protecting the key characteristics.

**Shifting "fashion" in architecture.** Preferred styles shift with observations from past pain, ecosystem changes, new capabilities (containers, Kubernetes), acceleration, domain changes, technology changes, and external factors (licensing). Architects should track trends to know when to follow and when to make exceptions.

## Patterns
*(Styles are catalogued in PATTERN-CATALOG.md with their selection profiles.)*

**Style selection (method)** - Intent: choose the least-worst style for a given problem. When to use: at the start of design and at major restructuring points. Trade-offs: every style is a fixed bargain; the method (characteristics → quantum count → data placement → communication) reduces the choice to the few that fit and surfaces the trade-offs to defend in ADRs.

## Principles & best practices

- **Choose by characteristics, not fashion.** Match the prioritized top-three characteristics to the styles' star ratings, then weigh domain, data, organization, and operational maturity.
- **Default to the simplest style that meets the characteristics.** Layered/modular monolith for one quantum and tight budgets; reach for distribution only when differing characteristics or scale genuinely require it.
- **Use domain/architecture isomorphism.** Let the problem's shape suggest the style (customizable product → microkernel; massive variable concurrency → space-based; many discrete operations → space-based/pipeline).
- **Prefer synchronous communication by default**, asynchronous when scale/decoupling demand it.
- **Capture the why in ADRs and protect characteristics with fitness functions.**
- **Treat the modular monolith and service-based architecture as stepping-stones** toward microservices when full distribution is the eventual goal.
- **Match style to engineering maturity.** Microservices and event-driven styles assume automation, testing, and DevOps; imposing them on an immature organization creates friction.

## Anti-patterns & pitfalls

- **Architecture by implication / accidental architecture.** A team "just starts coding" and ends up in an unintended (usually layered) style without deciding deliberately.
- **Describing architecture by one dimension only.** "We use microservices" answers the style question but leaves characteristics, decisions, and components undefined.
- **Choosing microservices for a highly coupled domain.** A multi-page, context-dependent insurance workflow fits a less-coupled style (for example, service-based) far better than fine-grained microservices.
- **Over-distributing.** Adopting a distributed style for a single-quantum problem buys complexity with no benefit.
- **Ignoring operational reality.** Selecting a style whose success depends on automation the organization lacks.

## Decision guidance

1. **Derive and prioritize characteristics** (top three) from the domain and requirements.
2. **Count the quanta** - one set of characteristics → monolith candidates (layered, modular, microkernel, pipeline); multiple sets → distributed candidates (service-based, event-driven, microservices, space-based).
3. **Shortlist by star ratings** against the top-three characteristics.
4. **Apply the decision criteria** - domain, data architecture, organizational factors, process/team maturity, and domain/architecture isomorphism.
5. **Decide data placement and communication style** (synchronous by default).
6. **Prefer the simplest fit**, using modular monolith or service-based architecture as a stepping-stone when distribution is the destination.
7. **Record the decision and trade-offs in an ADR** and add fitness functions to protect the chosen characteristics.

## Source attribution

- The eight styles, their star-rating profiles, the style-vs-pattern distinction, monolith-vs-distributed framing, the Chapter 18 decision criteria (domain, characteristics, data, organizational factors, process maturity, domain/architecture isomorphism), the three determinations, "synchronous by default," and shifting architecture fashion all come from **Fundamentals of Software Architecture, Chapters 8–18 (Richards & Ford, 2020).**
- The four-dimensions framing, the house metaphor for hard-to-change style decisions, and per-style one-line strengths come from **Head First Software Architecture, Chapter 1 (Gandhi, Richards & Ford, 2023).**
- Service-based architecture as a stepping-stone to microservices and the quantum-count basis for distribution come from **Software Architecture: The Hard Parts (Ford et al., 2021).**
- The general catalog of styles (layered, client-server, microservices, SOA, EDA, hexagonal, component-based) and the iterative, trade-off-driven approach to selecting and combining them is corroborated by **Software Architecture for Developers (Abrams, 2024).**

## Recent developments (2024–2026)

- **Modular monolith is now a mainstream default, not a stepping-stone afterthought.** The 2024-2026 discourse strongly rehabilitated the modular monolith as a serious competitor to microservices, driven by simplicity, developer experience, and lower operational cost. The pragmatic consensus is "context, not sides": both microservices-everywhere and monolith-purism are treated as suboptimal. (Foojay, "Monolith vs Microservices in 2025," https://foojay.io/today/monolith-vs-microservices-2025/; Java Code Geeks, "Microservices vs. Modular Monoliths in 2025," https://www.javacodegeeks.com/2025/12/microservices-vs-modular-monoliths-in-2025-when-each-approach-wins.html)

- **Visible microservices consolidation.** Reporting in 2025 cited a CNCF survey finding that roughly 42% of organizations that adopted microservices had consolidated at least some services back into larger deployable units, citing debugging complexity, operational overhead, and network latency. This validates this file's "over-distributing" antipattern and the quantum-count discipline as a live, costly mistake teams are now reversing. (SoftwareSeni, "From Microservices Consolidation to Modular Monoliths," 2025, https://www.softwareseni.com/understanding-modern-software-architecture-from-microservices-consolidation-to-modular-monoliths/)

- **Cell-based architecture as a resilience style.** Cell-based architecture (each cell a complete, independently operable instance of the workload) gained traction as a fault-isolation pattern that limits blast radius, formalized in AWS Well-Architected guidance and an InfoQ minibook. It sits alongside the eight classic styles as a distribution choice specifically optimized for resilience and scope-of-impact reduction. (AWS Well-Architected, "Reducing the Scope of Impact with Cell-Based Architecture," 2024-2025, https://docs.aws.amazon.com/wellarchitected/latest/reducing-scope-of-impact-with-cell-based-architecture/reducing-scope-of-impact-with-cell-based-architecture.html; InfoQ minibook, 2024, https://www.infoq.com/minibooks/cell-based-architecture-2024/)

- **AI-assisted style selection and decomposition.** LLMs are increasingly used to analyze a codebase and recommend service boundaries or a target style, shifting some of the "count the quanta / pick the style" work toward AI-assisted analysis while keeping the architect accountable for the trade-off judgment. (Augment Code, "AI-Driven Tactics to Speed Monolith-to-Microservices Migration," 2025, https://www.augmentcode.com/guides/15-ai-driven-tactics-to-speed-monolith-to-microservices-migration)

- **The selection method is unchanged.** The characteristics-first, count-the-quanta, data-placement, synchronous-by-default method from Richards & Ford remains the durable framework; the 2024-2026 shift is the reset of the "fashion" pendulum toward modular monoliths and a new resilience-focused distributed style (cell-based).
