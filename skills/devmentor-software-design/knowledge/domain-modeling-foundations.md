# Domain Modeling Foundations: Domain Science and Engineering

**Domain:** 06 - Software Design Patterns and DDD
**Primary sources:** Domain Science and Engineering: A Foundation for Software Development (Dines Bjørner, 2021)

## Overview
Domain science and engineering (DS&E) is a rigorous, formal approach to understanding and describing the world a software system will operate in, *before* requirements are written and long before code is designed. Where domain-driven design (Evans, Vernon, Khononov) is a pragmatic engineering discipline for aligning code with the business, Bjørner's domain science is closer to a natural science of human-made domains: it asks what a domain *is*, provides a systematic method ("prompts" and steps) for analyzing and describing one, and shows how requirements and ultimately software can be derived from a domain description in a traceable way. The motivation is the same as DDD's, that software fails when it misunderstands its domain, but the treatment is formal and theory-first.

The organizing idea is the **Triptych**: software development proceeds from a **Domain description (D)** to a **Requirements prescription (R)** to a **Software specification (S)**, with the desired relation that the software running on a machine, together with the domain, satisfies the requirements (informally, D, S ⊨ R). The point of substance for practitioners: requirements can be *systematically derived* from a well-formed domain description rather than invented, and the domain description itself is a reusable scientific artifact. This sits early in the SDLC, upstream of requirements engineering and architecture, and complements (rather than competes with) DDD's ubiquitous-language and bounded-context work. (Bjørner 2021, ch. 1, ch. 9–11)

This file keeps the formalism accessible: the value for a software team is the *method and vocabulary*, not the RSL (a formal specification language) notation in which Bjørner's examples are written.

## Core concepts

### Domain, model, and the role of boundaries
A **domain** is a universe of discourse (UoD): the part of the world the team chooses to study, given a name (a "sort"). Domain analysis and description produce a *model* that describes a usually infinite set of domain instances, not a single instance. As in DDD, choosing the **boundary** of the domain (what is inside, what is the external context) is crucial and not obvious. A narrow boundary is chosen when the goal is to feed a specific requirements effort; a wider boundary when the goal is to research a domain for future use. Analysis can proceed top-down (most general aspects first) or bottom-up (atomic entities outward); Bjørner's own presentation is top-down. (Bjørner 2021, ch. 4)

A domain description, unlike a program, cannot be formally *verified* against the real world. It can only be *validated* against it by observation, and individual properties of the description can be stated and proved within the description and then checked against reality. (Bjørner 2021, ch. 10)

### Entities: endurants and perdurants
The foundational taxonomy of what exists in a domain:

- **Endurants** - entities that exist wholly at any moment in time: the "things" of the domain (a road net, hubs, links, vehicles, a railway line, a station). Endurants are what most readily present themselves for observation.
- **Perdurants** - entities that unfold over time: actions, events, and behaviours. There is a "transcendental" move from endurants to perdurants: the things give rise to the processes they participate in (a vehicle endures; a journey is a perdurant).

This endurant/perdurant split is the formal cousin of DDD's data-versus-behavior and entity-versus-process distinctions. (Bjørner 2021, ch. 4–7)

### External vs internal qualities of endurants
Endurants are analyzed along two axes:

- **External qualities** - the manifest structure: what can be touched or seen, and the abstract structures built from them. Endurants are taxonomized as **discrete** (parts) vs **continuous** (fluids/materials), and parts as **atomic** (no proper sub-parts of interest) vs **composite** (containing sub-parts), plus structures and aggregates of parts. (Bjørner 2021, ch. 4)
- **Internal qualities** - three kinds (Bjørner 2021, ch. 5):
  - **Unique identifiers** - every part has a unique identity (echoing the DDD entity-identity idea, but made universal and formal).
  - **Mereology** - the theory of part-whole and part-part relations: how parts are connected and composed within a whole. This is how the model captures topology and relationships (which hubs a link connects, which stations a line joins).
  - **Attributes** - the properties of a part (its values, states, measurements), distinguished from its identity and its parthood.

### Analysis prompts, description prompts, and method steps
The method is operationalized as a calculus of **prompts** the analyst applies to the domain:
- **Analysis prompts** ask yes/no or classifying questions about an entity (Is it an endurant or perdurant? Discrete or continuous? Atomic or composite?).
- **Description prompts** then generate the corresponding piece of the formal description (for example, "name and sketch the universe of discourse"; observe the sub-parts of a composite; describe unique identifiers, mereology, attributes).
- **Method steps** sequence the work (Step 1: select the domain of interest and name it; Step 2: determine external qualities; and so on).

Producing the rough sketch of a domain is non-trivial: it takes repeated trial sketches, site visits, conversations with professionals at all levels, and literature/Internet research. The danger Bjørner stresses repeatedly: if you do not painstakingly narrate *and* formalize what every term means, downstream readers (designers) will make erroneous assumptions, the same risk DDD addresses with the ubiquitous language. (Bjørner 2021, ch. 4)

### Domain facets
A **domain facet** is one of a finite set of generic *views* of a domain; together the facets cover the domain, individually they cover conceptually different aspects. Each facet section defines the facet, analyzes its concepts with examples, speculates on the requirements it can yield, and comments on how to model it. The seven facets identified so far (Bjørner 2021, ch. 8):

1. **Intrinsics** - the phenomena and concepts basic to all other facets; what the core analysis (endurants and their perdurants) naturally produces, covering at least one named stakeholder view.
2. **Support technologies** - the technologies that realize domain phenomena (railway signaling, sensors, actuators), including their failure modes and the requirements they imply.
3. **Rules and regulations** - the prescriptive constraints under which the domain operates; closely related to scripts and license languages.
4. **Scripts** - structured, near-formal (sometimes outright formal) prescriptions of procedures the domain follows.
5. **License languages** - formalizations of permissions, obligations, and rights (who may do what with what).
6. **Management and organization** - the structures of authority, responsibility, and coordination in the domain.
7. **Human behaviour** - the range of human action from diligent to sloppy to malicious, which the domain (and any software supporting it) must account for.

Facets give a checklist that pushes analysis beyond the obvious "things and actions" into the rules, technologies, organization, and human factors that determine how a domain actually behaves, an area DDD treats less systematically.

### From domain descriptions to requirements
The most directly useful contribution for SDLC practice. Bjørner classifies requirements by the vocabulary they use (ch. 1, ch. 9, ch. 10):

- **Domain requirements** - expressible solely in terms of domain concepts (endurants, actions, events, behaviours).
- **Interface requirements** - expressible in a combination of domain and machine concepts, arising wherever an entity is *shared* between the domain and the machine (initialization and ongoing update of machine state from shared domain endurants; interaction during an action; machine responses to domain events and vice versa; monitoring and control).
- **Machine requirements** - expressible solely in machine concepts (for example, display units, performance, dependability).

Domain requirements are *derived* from a domain description by five operations applied with requirements stakeholders:

- **Projection** - sub-setting the description: drop the parts the stakeholders judge irrelevant to the requirements (for example, omit road-wear attributes).
- **Instantiation** - specializing/refining abstract endurants, actions, events, and behaviours into more concrete ones (for example, model only freeways, or only road-pricing nets).
- **Determination** - making the description less (or sometimes more) non-deterministic (for example, introduce traffic signals that constrain intersection states to red/yellow/green).
- **Extension** - adding phenomena and concepts that were not feasible without information technology (for example, GPS gadgets on buses that report position to a central computer).
- **Fitting** - harmonizing/reconciling several domain-requirements descriptions so they compose (across facets or across overlapping requirements).

Interface requirements themselves are further developed using projection, instantiation, determination, extension, and fitting on the shared entities. This gives a disciplined, traceable path from a validated picture of the world to what the machine must do. (Bjørner 2021, ch. 9–10)

### Demos, simulators, monitors, controllers
A domain description can be interpreted to yield different machine kinds: a **demo** or **simulator** (the machine stands in for the domain), a **monitor** (the machine observes the domain), and a **controller** (the machine observes and actuates the domain, with actuators placed in the domain). Each kind shapes which interface requirements matter. (Bjørner 2021, ch. 10)

## Frameworks & models
- **The Triptych: Domain → Requirements → Software (D → R → S).** The spine of the method; requirements and software are developed from, and stay traceable to, the domain description, with the satisfaction relation D, S ⊨ R as the goal. (Bjørner 2021, ch. 1, ch. 10–11)
- **Entity taxonomy: endurants (discrete/continuous; atomic/composite) and perdurants (actions/events/behaviours).** A complete classification scheme for "what is in the domain." (Bjørner 2021, ch. 4–7)
- **Internal-qualities triad: unique identifiers, mereology, attributes.** How to describe any part fully. (Bjørner 2021, ch. 5)
- **Seven domain facets** as a coverage checklist for analysis. (Bjørner 2021, ch. 8)
- **Requirements-derivation operations: projection, instantiation, determination, extension, fitting.** A repeatable procedure for turning a domain description into domain and interface requirements. (Bjørner 2021, ch. 9)
- **Prompt-and-step calculus** (analysis prompts, description prompts, method steps) for actually conducting and recording a domain analysis. (Bjørner 2021, ch. 4–7)

## Patterns
DS&E is a method rather than a pattern catalog, but several reusable, named procedures function as patterns. For each: intent; when to use; trade-offs.

- **Triptych development (D → R → S)** - Intent: derive requirements and software from a validated domain description so artifacts stay traceable to the world. When to use: high-assurance, long-lived, or safety-relevant domains (transport, railways, pipelines, finance) where misunderstanding the domain is expensive. Trade-offs: substantial up-front modeling and formal-method skill; the domain description cannot be formally verified, only validated.
- **Endurant/perdurant analysis** - Intent: classify everything in scope as enduring things vs unfolding processes, then describe each. When to use: at the start of any rigorous domain study. Trade-offs: thorough and unambiguous; heavier than informal DDD discovery.
- **Internal-qualities description (identity + mereology + attributes)** - Intent: describe each part by its unique identity, its part-whole/part-part relations, and its attributes. When to use: whenever relationships and topology matter (networks, supply chains, org structures). Trade-offs: captures structure precisely; verbose.
- **Facet-driven analysis** - Intent: use the seven facets as a checklist so analysis covers rules, technologies, organization, and human behaviour, not just things and actions. When to use: any domain analysis at risk of missing non-obvious constraints. Trade-offs: comprehensive coverage; can expand scope.
- **Requirements derivation (projection / instantiation / determination / extension / fitting)** - Intent: mechanically transform a domain description into domain and interface requirements with stakeholders. When to use: when requirements must be traceable to a domain model and justified rather than invented. Trade-offs: requires an existing formal domain description; rigorous and auditable but front-loaded.

## Principles & best practices
- **Narrate and formalize every term.** Informal narrative plus formal description together prevent designers from making erroneous assumptions, the formal-methods analogue of the ubiquitous language. (Bjørner 2021, ch. 4)
- **Describe a set of instances, not one.** A domain model captures all sensible instances of the domain, not a single configuration. (Bjørner 2021, ch. 1)
- **Choose the boundary deliberately**, narrow for a specific requirements effort, wider for research; record what is context (outside) versus domain (inside). (Bjørner 2021, ch. 4)
- **Validate, do not expect to verify, the domain description**; check properties against observation of the real domain. (Bjørner 2021, ch. 10)
- **Derive requirements from the domain**, applying the five operations with stakeholders, rather than gathering requirements in a vacuum. (Bjørner 2021, ch. 9)
- **Cover all facets** before concluding the analysis is complete. (Bjørner 2021, ch. 8)

## Anti-patterns & pitfalls
- **Relying on a priori understanding of terms** without narrating and formalizing them; the explicit "danger" Bjørner names, leading to silent wrong assumptions downstream.
- **Treating the domain description as verifiable truth** instead of a model to be validated against reality.
- **Stopping analysis at intrinsics** (things and actions) and missing the rules, support technologies, organizational, and human-behaviour facets that govern real behaviour.
- **Inventing requirements** disconnected from any domain description, losing traceability and justification.
- **Picking the boundary by accident**, pulling in too much or too little for the intended purpose.

## Decision guidance
- **Use DS&E when rigor pays off:** long-lived, regulated, or safety-/mission-critical domains where the cost of misunderstanding the world is high, and where a reusable, formal domain description will be amortized across multiple requirements and software efforts. For ordinary business software, DDD's lighter ubiquitous-language and bounded-context practices are usually sufficient; DS&E can still contribute its facet checklist and its requirements-derivation operations without full formalization.
- **Sequence the work** as Triptych: build and validate the domain description first; derive domain and interface requirements via projection/instantiation/determination/extension/fitting; then specify software. Keep traceability D → R → S.
- **Pick the boundary by intent** (narrow for requirements, wide for research) and record the external context explicitly.
- **Drive analysis with prompts and steps**, and force coverage with the seven facets, recording both narrative and formal description for every entity.
- **Choose the machine kind** (demo/simulator, monitor, controller) early, since it determines which interface requirements dominate.

## Source attribution
- Triptych (D → R → S), general vocabulary, requirements classification (domain/interface/machine): Bjørner 2021, ch. 1.
- Domain, model, boundary, top-down/bottom-up, external qualities, description prompts and method steps: Bjørner 2021, ch. 4.
- Internal qualities (unique identifiers, mereology, attributes): Bjørner 2021, ch. 5.
- Endurants and perdurants (actions, events, behaviours), transcendental move: Bjørner 2021, ch. 4–7.
- Seven domain facets (intrinsics, support technologies, rules and regulations, scripts, license languages, management and organization, human behaviour): Bjørner 2021, ch. 8.
- Requirements and the derivation operations (projection, instantiation, determination, extension, fitting), interface vs machine requirements: Bjørner 2021, ch. 9.
- Demos/simulators/monitors/controllers, validation vs verification: Bjørner 2021, ch. 10.
- Method reflection and on conducting a project: Bjørner 2021, ch. 11.

## Recent developments (2024–2026)
- **AI is poised to move formal methods from fringe to mainstream.** Martin Kleppmann predicts (Dec 2025) that LLMs will make formal verification broadly practical by collapsing the long-standing "specification bottleneck", the manual effort of translating natural-language requirements into precise formal specs, which is exactly the gap Bjorner's narrate-and-formalize discipline targets. (Martin Kleppmann's blog, "Prediction: AI will make formal verification go mainstream", 2025, https://martin.kleppmann.com/2025/12/08/ai-formal-verification.html)
- **LLM-assisted specification and domain modeling is an active research area.** 2025-2026 work generates formal specifications and invariants from natural language or code, validates Alloy-style domain models with LLM-generated test cases, and benchmarks AI on formally modeling real-world systems (e.g., SysMoBench), giving Bjorner's prompt-and-step calculus an automation path. (Springer, "Validating Formal Specifications with LLM-Generated Test Cases", 2025/2026, https://link.springer.com/chapter/10.1007/978-3-032-26204-2_15; arXiv, "SysMoBench", 2025, https://arxiv.org/pdf/2509.23130)
- **"Vericoding" emerges as a named practice.** Using LLMs to produce formally verified code (proof scripts, annotations such as ACSL) became a recognized technique in 2025, lowering the skill barrier that historically kept Triptych-style rigor out of ordinary projects. (arXiv, "Automatic Generation of Formal Specification and Verification Annotations Using LLMs", 2026, https://arxiv.org/pdf/2601.12845)
- **Persistent caveat: AI does not remove validation risk.** Researchers stress that ambiguity, missing domain models, and LLM instability still make consistent formalization hard, echoing Bjorner's point that a domain description can only be validated against reality, not verified, and that erroneous assumptions propagate silently. (arXiv VERIFYAI/position papers, 2025-2026)
- **The DS&E framework itself is stable.** Bjorner's endurant/perdurant taxonomy, internal-qualities triad, seven facets, and requirements-derivation operations are unchanged; the development is tooling (LLM-assisted formalization) that could make this rigor cheaper to adopt outside safety-critical niches.
