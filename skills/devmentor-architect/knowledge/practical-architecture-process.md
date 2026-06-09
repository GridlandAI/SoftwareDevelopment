# Practical Architecture Process

**Domain:** 04 - Software Architecture Fundamentals
**Primary sources:** Software Architecture and Design: The Practical Guide (Köhler, 2025); Designing Software Architectures, 2nd ed. (Cervantes & Kazman, 2024); Hands-on Software Architecture (Kim & Kim, 2025); Just Enough Software Architecture (Fairbanks, 2010)

## Overview
This file describes a pragmatic, end-to-end architecture-and-design workflow: from requirements and quality goals, through choosing styles and building blocks, to documenting decisions, reviewing, and handing off to implementation. Where the methods file covers formal step-by-step processes (ADD, UAP) and the risk-driven file covers "how much," this file is the working architect's playbook - the recurring tasks, the documentation structures (arc42, C4, ADRs), and the practical heuristics and checklists that make architecture useful day to day. Its center of gravity is Köhler's practitioner-oriented guidance, framed around Simon Brown's view that an architect must not only decide but also (co-)implement, and that "the code doesn't tell the whole story."

The throughline: a software architecture defines the fundamental structure that breaks complexity into manageable parts, ensures components work together, implements both functional and non-functional requirements, and - per Robert C. Martin - minimizes the human effort required to build and maintain the system.

## Core concepts

- **Tasks of a software architecture.** Structuring (decompose into modules/layers); design principles (guidelines that steer all decisions); communication between modules (interfaces and data exchange); implementing requirements (functional and non-functional); technological decisions (languages, frameworks, tools); adaptability (easy to extend and scale); integration with other systems; documentation and communication (shared understanding across stakeholders).
- **Quality feature / quality goal.** A non-technical quality the software must fulfill (performance, stability, security, maintainability, capability for incremental updates). For each, answer three questions: which quality feature was addressed; what solution was chosen; why does that solution ensure the quality. Express quality goals as concrete scenarios with measurable values ("the system must return a response in a maximum of 100 ms").
- **Quality tree with scenarios.** The structure (from ATAM) used in arc42 section 10 to lay out competing quality attributes and the scenarios that concretize vague requirements.
- **Building block.** A static structural element of the system: modules, components, subsystems, classes, interfaces, packages, libraries, frameworks, layers, partitions. The building block view shows the static breakdown and refines the context (where the whole system is a single box). White boxes expose internals; black boxes hide them; levels add detail progressively.
- **Architectural decision.** A consciously made, documented choice (technology stack, architectural style, component/interface identification, data modeling, integration, security mechanisms), recorded with rationale and rejected alternatives so it can be retraced. Unconscious decisions cause unclear structures and unintended dependencies.
- **Cross-cutting concept.** A regulation/solution affecting multiple building blocks (business models, the chosen style, patterns used, technology/library usage rules, implementation rules).
- **Constraint.** A specification or framework condition restricting design/implementation freedom (data-privacy law, mandated technology stack), best split into technical, organizational, and political.

## Frameworks & models

### The architect's recurring workflow (end to end)
Synthesized from Köhler (architect responsibilities + arc42 walkthrough), aligned with ADD/UAP/risk-driven:

1. **Understand the business and technical context.** Identify functional and non-functional requirements (often vague), record constraints, evaluate their architectural impact, and communicate effects. Define context and scope from a business/domain perspective; identify stakeholders and their expectations.
2. **Set quality goals.** Elicit the most important quality requirements and express them as a quality tree with measurable scenarios; for each, capture the chosen solution and why it works.
3. **Define a viable solution strategy.** Make the fundamental decisions that frame everything else: technology stack, top-level modularization, the formative architectural style(s) and patterns, decisions on the most important quality features, and relevant organizational decisions.
4. **Design the building blocks.** Produce the building block view (level 1 subsystems/components and their interfaces and responsibilities; level 2 white-box internals where needed), the runtime view (critical scenarios as text or sequence diagrams), and the deployment view (nodes, environments, networks, artifact allocation).
5. **Address cross-cutting concerns and risks.** Document cross-cutting concepts; maintain a prioritized list of risks and technical debt, ideally with suggested solutions so they can be scheduled.
6. **Record decisions.** Capture each significant architectural decision with rationale and rejected alternatives (ADRs / arc42 section 9).
7. **Deal with technical challenges and risks.** Make decisions, then question, validate, and adapt them; proactively identify and mitigate issues early - many surface only once a technology is actually used.
8. **Further develop the architecture.** Treat it as living, not static; adjust earlier decisions as requirements change or assumptions prove wrong; stay continuously involved.
9. **Participate in development and assure quality.** Co-implement; run regular code reviews; set and check technical standards; continuously review and validate the architecture; make the design decisions that ensure performance, security, scalability. Quality is a shared, continuously monitored goal, not one person's job.

### arc42 - standardized documentation structure (Starke & Hruschka)
A twelve-section template (open source, the basis the iSAQB curriculum promotes) that can serve both as a documentation structure and as a process model. Not every section is always needed.
1. **Introduction and Goals** - fundamental requirements, top quality goals, key stakeholders and expectations.
2. **Constraints** - everything that limits design/implementation freedom (technical, organizational, political).
3. **Context and Scope** - system boundaries and external interfaces, from a business and (if relevant) technical perspective.
4. **Solution Strategy** - the fundamental decisions and approaches (see step 3 above).
5. **Building Block View** - static decomposition into components and relationships, hierarchical, white/black boxes.
6. **Runtime View** - important/critical runtime scenarios and interactions.
7. **Deployment View** - hardware, infrastructure, environments, and artifact allocation.
8. **Cross-cutting Concepts** - concerns spanning multiple building blocks.
9. **Architectural Decisions** - major decisions with justification and rejected alternatives.
10. **Quality Requirements** - quality tree with scenarios (ATAM structure), concretizing vague requirements with measurable values.
11. **Risks and Technical Debt** - prioritized, transparent list, optionally with suggested solutions.
12. **Glossary** - domain and technical terminology (common language).

### C4 model - hierarchical diagrams (Simon Brown)
A lightweight visualization with four levels of abstraction, each named with a C, designed to fix ambiguous box-and-line diagrams ("diagrams are the maps that help developers navigate a codebase"). It can be used as an alternative to arc42's diagrams.
- **C1 System Context** - how the software system fits into the surrounding landscape (people and external systems).
- **C2 Container** - the system as coarse-grained, technical containers (apps, services, data stores) and their relationships.
- **C3 Component** - the internal components of a single container.
- **C4 Code** - implementation of a component, e.g., as a UML class diagram.
Multiple views can be drawn per level, each showing one section.

### Architecture Decision Records (ADRs) and Doc-as-Code
Record decisions where they can be discussed and communicated at any time: the decision, its justification, the alternatives considered, and why they were rejected, including negative experiences to learn from. Köhler advocates Doc-as-Code - keeping documentation in the repository with text-based, diff-able, generatable tooling: **AsciiDoc** for prose, **PlantUML** for diagrams-as-text, and **Structurizr** for C4 models as code. Tie documentation updates into the team's Definition of Done.

## Patterns
These are the practical documentation and process patterns the workflow relies on. (See PATTERN-CATALOG.md.)

**arc42 template** - *Intent:* a standardized, twelve-section structure for architecture documentation and communication. *When to use:* any project wanting a consistent, reusable, gap-visible documentation home; especially where a shared structure speeds onboarding across projects. *Trade-offs:* not all sections fit every project (omit unneeded ones); value depends on keeping it current.

**C4 model** - *Intent:* unambiguous, hierarchical architecture diagrams at four zoom levels with a standard vocabulary. *When to use:* communicating structure to mixed audiences; pairs well with Doc-as-Code via Structurizr. *Trade-offs:* focuses on structure/diagrams, not the full decision record (combine with ADRs/arc42).

**Architecture Decision Record (ADR)** - *Intent:* capture a single significant decision with context, options, choice, and consequences. *When to use:* whenever a non-trivial, hard-to-reverse choice is made (stack, style, integration, security). *Trade-offs:* requires discipline to keep writing them; stale ADRs mislead.

**Doc-as-Code** - *Intent:* treat documentation as source artifacts (AsciiDoc/PlantUML/Structurizr) versioned with the code. *When to use:* teams wanting docs that stay in sync, are reviewable in PRs, and are generatable. *Trade-offs:* toolchain setup and learning curve.

## Principles & best practices
Seven rules for good architecture documentation (Clements et al., via Köhler):
- **Write from the reader's point of view.** Know the target group; structure information; use clear terminology; do not overload with acronyms (define them in the glossary).
- **Avoid unnecessary repetition** (one source of truth; repeat only to add a different perspective).
- **Avoid ambiguity** - prefer standardized notations (UML, C4) over box-and-line diagrams whose arrows could mean anything; indicate requirement levels (RFC 2119 "must / should / may").
- **Use a standardized structure** (e.g., arc42) so readers find information and authors can fill sections incrementally and see what is missing.
- **Keep track of decisions and their underlying reasons** - record every important decision with justification and rejected alternatives.
- **Keep the documentation up to date, but not too up to date** - update as a fixed point in the process (Definition of Done), but do not document throwaway experiments immediately.
- **Check the documentation for suitability for use** - have the target group review it regularly.

Further practitioner principles:
- Be a hands-on architect, not an ivory-tower one: co-implement and participate in development.
- Document fundamental early decisions and communicate them as soon as possible to prevent duplicated, divergent approaches.
- Concretize vague quality requirements into measurable scenarios.
- Make technology and style choices that fit the requirements; in larger systems combine several styles at different levels.
- Treat the architecture as living; validate and evolve it continuously.

## Anti-patterns & pitfalls
- **Box-and-line diagrams without defined semantics** - an arrow that might mean a synchronous call, instantiation, async message, or inheritance; the classic whiteboard ambiguity C4 was created to fix.
- **Ivory-tower architect** - making decisions without collaborating on or implementing them.
- **Unconscious / undocumented decisions** - leading to unclear structures, unintended dependencies, and harder-to-change, less stable, less secure software.
- **Outdated documentation** - once readers find it wrong, they abandon it and research elsewhere, generating extra work.
- **Duplicated documentation** - the same content in multiple places drifts and you cannot tell which is authoritative.
- **Acronym/jargon overload** without a glossary; documentation written for the author rather than the reader.
- Köhler's catalogued code/architecture anti-patterns to avoid in the building blocks: Big Ball of Mud, God Object, Spaghetti Code, Reinventing the Wheel, Cargo Cult Programming.

## Decision guidance
- **arc42 vs. C4.** They are complementary, not exclusive: use arc42 as the overall documentation structure (it covers decisions, quality, risks, glossary) and C4 for the diagrams within it (context, container, component, code). C4 alone can substitute for arc42's diagrammatic parts when a lightweight approach is wanted.
- **How much to document.** Document for a purpose and a target group; standardized structure shows what is still missing. Align with the risk-driven principle (Fairbanks): build narrow models where risks live; do not default to comprehensive documentation. Keep it current via the Definition of Done.
- **Choosing an architectural style.** Depends on the requirements; expect to combine styles at different levels in larger systems. Layered/client-server for classic tiered IT; microservices for independently deployable/scalable business areas; event-driven for loosely coupled, asynchronous reactions. (Style and pattern catalogs live in the architecture-patterns and DDD files.)
- **Recording a decision.** When the choice is significant and hard to reverse, write an ADR (or fill arc42 section 9): state the decision, the rationale, the alternatives considered, and why they were rejected - including lessons from negative experiences.
- **Tooling.** Prefer Doc-as-Code (AsciiDoc + PlantUML + Structurizr) so documentation is versioned, reviewable, and generatable alongside the code.
- **Bridging to formal methods.** This pragmatic workflow maps onto ADD (drivers → design concepts → structures → preliminary docs → analysis) and UAP (A1 requirements refinement → A2 context → A3 styles → A4 views → A5 NFRs → A6 evaluation); arc42 sections correspond closely to the artifacts those methods produce (context, building blocks, runtime, deployment, decisions, quality, risks).

## Source attribution
- Tasks of a software architecture, role/responsibilities of the architect, documenting quality features, the seven documentation rules, arc42 twelve sections, C4 four levels, ADRs and Doc-as-Code (AsciiDoc/PlantUML/Structurizr), code/architecture anti-patterns: Software Architecture and Design, chapters 1, 3, 5 (Köhler, 2025).
- Preliminary documentation during design, recording responsibilities and decisions with rationale, scenario-based documentation, the three documentation purposes (analysis/construction/education): Designing Software Architectures, 2nd ed., chapter 4 (Cervantes & Kazman, 2024).
- Standardized architecture description template aligned to the process, and the activity-based workflow (requirements → context → styles → views → NFRs → evaluation): Hands-on Software Architecture, chapter 2 (Kim & Kim, 2025).
- "Build narrow models focused on risks; documentation need not be comprehensive": Just Enough Software Architecture, chapters 3 and 7 (Fairbanks, 2010).

## Recent developments (2024–2026)

- **arc42 and Structurizr both shipped current releases.** arc42 reached version 9.0 (EN, July 2025) and the Structurizr CLI continues active releases through late 2025, keeping the arc42 + C4 + docs-as-code toolchain this file recommends current and well-maintained. (arc42 9.0, 2025, referenced via bitsmuggler arc42-c4 example, https://github.com/bitsmuggler/arc42-c4-software-architecture-documentation-example; Structurizr, https://structurizr.com/)

- **Docs-as-code became AI-friendly via MCP.** Structurizr's text-based DSL and model-based consistency make C4 diagrams generatable and validatable by AI; a Structurizr MCP server now provides DSL validation, parsing, and inspection so AI agents can read and modify the model directly. This turns "Doc-as-Code" into a substrate that LLMs can both author and check, reducing the staleness risk this file warns about. (IcePanel, "Top 9 tools for C4 model diagrams," 2025, https://icepanel.io/blog/2025-08-28-top-9-tools-for-c4-model-diagrams; Devot, "C4 Model Architecture with Structurizr," https://devot.team/blog/c4-model)

- **LLM-generated architecture documentation.** Research like CIAO ("Code In Architecture Out") demonstrates reconstructing architecture documentation and views from source code with LLMs, offering a path to the "redocument after each change" ideal without the manual burden, though generated docs still need human vetting. (arXiv, "CIAO," 2026, https://arxiv.org/pdf/2604.08293)

- **Process guidance reinforced by the consolidation trend.** The 2025 swing back to modular monoliths and "context, not fashion" matches this file's risk-driven, "combine styles at different levels, document for a purpose" stance, and Köhler's *Software Architecture and Design: The Practical Guide* (2025) is itself a recent entry in the practitioner-process literature. (Foojay, "Monolith vs Microservices in 2025," https://foojay.io/today/monolith-vs-microservices-2025/)

- **The documentation discipline is stable.** arc42's twelve sections, the C4 four levels, the seven documentation rules, and ADRs/Doc-as-Code are unchanged; the 2024-2026 development is tool releases plus AI-assisted diagram and document generation wired into the docs-as-code pipeline.
