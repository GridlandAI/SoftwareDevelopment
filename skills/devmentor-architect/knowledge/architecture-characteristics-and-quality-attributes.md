# Architecture Characteristics and Quality Attributes

**Domain:** 04 - Software Architecture Fundamentals
**Primary sources:** Fundamentals of Software Architecture (Richards & Ford, 2020); Head First Software Architecture (Gandhi, Richards & Ford, 2023); Software Architecture: The Hard Parts (Ford, Richards, Sadalage & Dehghani, 2021); Software Architecture for Developers (Abrams, 2024)

## Overview
Architecture characteristics are the non-domain concerns a system must support to succeed: the "-ilities" such as scalability, availability, performance, security, and maintainability. They are orthogonal to functionality. A system can implement every feature correctly and still fail because it cannot handle the expected load, cannot recover from failure, or cannot be changed safely. Richards and Ford define software architecture itself as the combination of four things: the structure (the architecture style), the architecture characteristics the system must support, the architecture decisions (rules), and the design principles (guidelines). Characteristics are the dimension that most directly shapes which style and decisions are appropriate.

Getting characteristics right matters across the whole SDLC. They are discovered during requirements and discovery work, they drive the choice of architecture style and data design, they constrain implementation, and they must be continuously governed as the system and its environment change. Because characteristics interact and compete, identifying and prioritizing them is fundamentally an exercise in trade-off analysis, which is the first law of software architecture.

## Core concepts

**Definition (three criteria).** Richards and Ford say an architecture characteristic meets three criteria simultaneously: (1) it specifies a non-domain design consideration; (2) it influences some structural aspect of the design; and (3) it is critical or important to application success. Security, for example, is present in every system, but it rises to the level of an *architecture* characteristic only when it requires special structure (for example, isolating payment processing in its own module) rather than ordinary coding hygiene.

**Naming.** The same idea travels under several names: non-functional requirements, quality attributes, system quality attributes, and "the -ilities." Richards and Ford prefer "architecture characteristics" because "non-functional" is self-denigrating and "quality attributes" implies after-the-fact assessment rather than up-front design. This note uses "characteristic" and "quality attribute" interchangeably.

**Explicit vs implicit characteristics.** Explicit characteristics appear in requirements documents (for example, an expected number of concurrent users). Implicit characteristics rarely appear in requirements yet are necessary for success (availability, reliability, and security underpin almost every system). Architects must use domain knowledge to surface implicit ones; a high-frequency trading firm knows low latency is critical even when no document states it.

**The four dimensions of architecture (Head First).** Head First frames architecture as four interconnected dimensions: architecture characteristics, architecture decisions, logical components, and architecture style. Characteristics are the foundation; you cannot choose a style or make sound decisions without first knowing which characteristics matter. "Our architecture is microservices" describes only one dimension and leaves the others unanswered.

## Frameworks & models

**The taxonomy: operational, structural, cross-cutting (Richards & Ford).**

- **Operational characteristics** - capabilities about how the system runs. Availability, continuity (disaster recovery), performance, recoverability, reliability/safety, robustness, scalability, elasticity. These overlap heavily with operations and DevOps concerns.
- **Structural characteristics** - code-structure and quality concerns the architect owns or co-owns. Configurability, extensibility, installability, leverageability/reuse, localization, maintainability, portability, upgradeability.
- **Cross-cutting characteristics** - important constraints that defy easy categorization. Accessibility, archivability, authentication, authorization, legal/regulatory, privacy, security, supportability, usability.

**Scalability vs elasticity.** A subtle but important distinction. Scalability is the ability to handle a growing number of concurrent users without serious degradation. Elasticity is the ability to handle sudden bursts of traffic. A hotel reservation system is scalable but rarely needs elasticity; a concert-ticket sale needs both. Many systems need one and not the other.

**Availability vs reliability.** These often overlap but differ. IP (the internet protocol) is available but not reliable: packets may arrive out of order and may need to be re-requested. Availability is about uptime/accessibility; reliability adds data integrity and correct, dependable behavior over time.

**The ISO/IEC 25010 quality model (referenced by Richards & Ford).** No complete industry standard exists, but ISO organizes quality into families: performance efficiency (time behavior, resource utilization, capacity), compatibility (coexistence, interoperability), usability (learnability, accessibility, error protection), reliability (maturity, availability, fault tolerance, recoverability), security (confidentiality, integrity, non-repudiation, accountability, authenticity), maintainability (modularity, reusability, analyzability, modifiability, testability), and portability (adaptability, installability, replaceability). ISO also lists functional suitability, which Richards and Ford argue is *not* an architecture characteristic but rather the motivating requirement.

**Translating domain concerns to characteristics (Richards & Ford).** Stakeholders speak in business terms; architects speak in -ilities. A translation table bridges them: mergers and acquisitions imply interoperability, scalability, adaptability, extensibility; time to market implies agility + testability + deployability; user satisfaction implies performance, availability, fault tolerance, testability, deployability, agility, security; competitive advantage implies agility, testability, deployability, scalability, availability, fault tolerance. A common trap is equating one ingredient with the whole concern (agility is not just one thing; it equals agility + testability + deployability).

**Composite characteristics.** Many desirable characteristics decompose into smaller, measurable ones. Agility, for example, breaks down into modularity, deployability, and testability. Unpacking composites is how architects move from vague terms to objective measures.

**Architecture katas (Ted Neward; adapted by Ford & Richards).** A training exercise for deriving characteristics from a domain description plus "additional context." Each kata has a Description, Users, Requirements, and Additional context. Working through requirements line by line, separating explicit from implicit characteristics, is the core technique for identifying what a system actually needs (illustrated by the Silicon Sandwiches and Going, Going, Gone case studies).

## Patterns
*(Characteristics are not patterns per se; the relevant reusable practices are governance mechanisms. See PATTERN-CATALOG.md for the fitness-function entry.)*

**Architecture fitness function** - Intent: provide an objective integrity assessment of one or more architecture characteristics so they can be governed automatically as the system evolves. When to use: any time a characteristic is important but not urgent (modularity, layering rules, page-load budgets, cyclic-dependency avoidance) and is therefore likely to erode under day-to-day pressure. Trade-offs: requires up-front effort and developer buy-in; can give false confidence if the assertion is weak; must be understood by the team, not imposed from an ivory tower.

## Principles & best practices

- **First Law of Software Architecture: everything is a trade-off (Richards & Ford).** Improving one characteristic almost always costs another. Strengthening security typically degrades performance (more encryption, indirection). Choosing characteristics is balancing competing forces, like flying a helicopter where every control affects the others.
- **Aim for the least-worst architecture, not the best.** There is no architecture that maximizes every characteristic. Over-specifying is as damaging as under-specifying because each supported characteristic adds design complexity. The cautionary tale is the warship Vasa, which sank because it was over-specified.
- **Keep the list short; pick the top three.** Rather than ranking every characteristic (a near-impossible consensus exercise), have stakeholders choose the three most important in any order. This is easier to agree on and fosters the right trade-off conversations.
- **Use a litmus test for "characteristic vs domain."** Ask: does it require domain knowledge to implement, or is it an abstract, reusable concept? "Elasticity" is abstract and applies to any domain; a "reputation index" needs a subject-matter expert and is domain functionality.
- **Define characteristics objectively and organization-wide.** Vague terms ("performance," "agility") mean different things to different teams. Concrete, shared definitions create a ubiquitous language and let composites be decomposed into measurable parts.
- **Measure with nuance.** Performance is not one number: track maximum response times to catch outliers, set performance budgets (first contentful paint, first CPU idle), and use statistical models rather than arbitrary thresholds.
- **Scope characteristics at the quantum, not the system (Richards & Ford; Hard Parts).** Modern systems no longer have one set of characteristics. Different architecture quanta can need different characteristics (the auctioneer and the bidders in Going, Going, Gone need different availability), which often forces a distributed or hybrid architecture.

## Anti-patterns & pitfalls

- **Generic-architecture anti-pattern.** Trying to support all characteristics produces an unwieldy, over-engineered system that solves no problem well.
- **Over-specifying brittleness.** Making the system fail when a non-critical integration (a traffic service) is down, instead of degrading gracefully.
- **Ivory-tower governance.** Imposing fitness functions or characteristics that developers do not understand or agree with.
- **Treating characteristics as system-wide axioms.** Assuming "scalability" applies uniformly across the whole system when different parts have different needs.
- **Confusing structural decay for stability.** Failing to continually analyze the architecture, so coding changes silently erode characteristics like performance and availability over time (architecture vitality).
- **Equating one ingredient with a composite concern** (treating "performance" as "time to market" instead of agility + testability + deployability).

## Decision guidance

When reasoning about characteristics, an agent should:

1. **Separate domain from characteristics.** First capture what the system must *do*, then ask what non-domain qualities make those features successful.
2. **Surface implicit characteristics from the domain.** Look past the requirements document for availability, reliability, security, and domain-specific needs (bursty traffic, regulatory constraints, latency sensitivity).
3. **Translate stakeholder language into -ilities** using the domain-concern mapping, and watch for composite concerns.
4. **Prioritize ruthlessly.** Get the list to the top three. Ask "if we had to drop one, which would it be?" to test true necessity.
5. **Check the scope.** Determine whether one set of characteristics suffices (favoring a monolith) or whether different parts need different characteristics (forcing a distributed/hybrid design). This is the architecture-quantum question.
6. **Plan to measure and govern.** For each chosen characteristic, define an objective measure and, where possible, a fitness function wired into CI so the characteristic is protected as the system changes.
7. **Expect trade-offs.** If a decision appears to have no downside, the trade-off has not been found yet (Corollary 1 to the First Law).

## Source attribution

- The three-criteria definition, the operational/structural/cross-cutting taxonomy, explicit vs implicit characteristics, the ISO list, the domain-to-characteristic translation table, scalability vs elasticity, least-worst architecture, the Vasa, the top-three prioritization heuristic, the litmus test, architecture katas, measuring/governing, fitness functions, and the architecture-quantum scoping all come from **Fundamentals of Software Architecture, Chapters 1, 4, 5, 6, 7 (Richards & Ford, 2020).**
- The four-dimensions framing (characteristics, decisions, logical components, style) and "characteristics are the foundation" come from **Head First Software Architecture, Chapter 1 (Gandhi, Richards & Ford, 2023).**
- Quantum-level scoping of characteristics and the differing-characteristics-force-distribution insight are reinforced in **Software Architecture: The Hard Parts, Chapters 2–3 (Ford et al., 2021).**
- The general framing of quality attributes as non-functional requirements requiring trade-offs and iterative architectural decision making is corroborated by **Software Architecture for Developers (Abrams, 2024).**

## Recent developments (2024–2026)

- **Sustainability / carbon is emerging as a first-class architecture characteristic.** The Green Software Foundation's Software Carbon Intensity (SCI) specification was ratified as an international standard, ISO/IEC 21031:2024, giving architects an objective, measurable carbon metric to treat like any other "-ility" and to wire into fitness functions. SCI focuses architectural decisions on carbon awareness, energy efficiency, and hardware efficiency rather than local micro-optimizations. (Green Software Foundation, SCI specification, ISO/IEC 21031:2024, https://sci.greensoftware.foundation/; GSF SCI repo, https://github.com/Green-Software-Foundation/sci)

- **Carbon intensity is being extended to AI and the web.** In December 2025 the GSF ratified SCI for AI, addressing the distinctive energy/hardware profile of AI systems, and progressed an SCI for Web variant. This matters because AI workloads now dominate many systems' energy budgets, making sustainability a characteristic architects must scope at the quantum level just like scalability or availability. (GSF, "SCI for AI," 2025, https://sci-for-ai.greensoftware.foundation/; GSF, "Designing SCI for Web," 2025, https://greensoftware.foundation/articles/designing-sci-web-what-we-agreed-and-what-comes-next/)

- **Privacy engineering and sustainability called out as 2025 architecture trends.** The InfoQ 2025 trends report explicitly lists privacy engineering and sustainability as rising system-design concerns alongside AI, signaling that the operational/cross-cutting taxonomy in this file is expanding to include carbon and privacy as routinely-prioritized characteristics. (InfoQ Software Architecture and Design Trends Report 2025, https://www.infoq.com/articles/architecture-trends-2025/)

- **Resilience characteristics pushed by cell-based architecture.** Fault isolation and blast-radius reduction (a refinement of availability/fault tolerance) gained prominence through cell-based architecture, codified in AWS Well-Architected guidance, reinforcing this file's point that different quanta need different availability and that resilience must be designed structurally. (AWS Well-Architected, "Reducing the Scope of Impact with Cell-Based Architecture," 2024-2025, https://docs.aws.amazon.com/wellarchitected/latest/reducing-scope-of-impact-with-cell-based-architecture/reducing-scope-of-impact-with-cell-based-architecture.html)

- **Definitions and taxonomy stable.** The three-criteria definition, operational/structural/cross-cutting taxonomy, ISO/IEC 25010 framing, top-three prioritization, and quantum scoping from Richards & Ford are unchanged; the 2024-2026 movement is the addition of carbon/sustainability and privacy to the working list of characteristics and stronger standardization of how to measure them.
