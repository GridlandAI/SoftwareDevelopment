# Architecture Conception and Specification

**Domain:** 04 - Software Architecture Fundamentals
**Primary sources:** Architectural Design: Conception and Specification of Interactive Systems (Vissers, Ferreira Pires, Quartel & van Sinderen, 2016); Designing Complex Software Architecture: Guidelines on Pattern Selection and Microservice Identification Methods (Lauber, 2024)

## Overview
This file covers the disciplined, academic end of architecture: how to *conceive* a design rigorously and *specify* it precisely, and how to make a defensible choice among complex architectural styles. Where the practitioner books treat patterns as starting points, these two sources go deeper. Vissers et al. give a foundational theory of design itself - what a design model is, what abstraction precisely means, why a design language matters, and a layered conceptual vocabulary (entities, interactions, behaviour, causality, services, protocols, reference models) for specifying interactive and distributed systems unambiguously. Lauber gives an evidence-based answer to "which complex style, and how do I get there," through a systematic literature review comparing SOA and microservices on quality criteria, plus a taxonomy and framework for microservice identification across greenfield and brownfield environments.

Conception and specification matter in the SDLC because design lives first in the designer's mind and must be externalized into a model and a language before it can be analyzed, communicated, validated, or built. Ambiguity at this stage propagates into every later phase. Managing architectural complexity - through abstraction, stepwise refinement, and quality-driven style selection - is what keeps large systems comprehensible and buildable.

## Core concepts

### From Vissers et al. (design theory and the specification vocabulary)
- **Design model.** "A model of an object represents certain properties of that object and is made for one or more specific purposes." A design model is created to represent, analyze, and evaluate properties *before* the object is made to exist. It is a passive representation: the creative act of conceiving the system stays in the designer's mind; the model only embodies it. Two representations coexist - the volatile mental image and the tangible model derived from it.
- **Model requirements.** A model is effective only if it has **preciseness** (exactly reflects the needed properties), **parsimony** (only the needed properties), **unambiguity** (one interpretation), and **clarity** (easily and intuitively understood). These four also make the model a good communication medium.
- **Abstraction (precise definition).** "An abstraction of an object reflects only aspects of that object that are essential for certain purposes while ignoring aspects considered irrelevant for those purposes." Abstraction is not vague: it becomes operational the moment you explicitly name which aspects are essential. It is the technique that "underlies and imbues" the whole approach.
- **Design language.** A language is an expressive means whose expressions can be conveyed to and interpreted by a person or machine. A property and the language expression of that property are different notions (the smell of a violet vs. the words "l'odeur des violettes"). Designs should be expressed in an *artificial* design language (Petri nets, the book's causality-relations language) rather than ambiguous natural language. The expression acts as a "window" through which interpreters view the designed object.
- **System: external vs. internal perspective.** A system can be viewed from outside (its *service* - what it does for its environment) or from inside (its *structure* - the parts and their interconnection). System is a *top-down recursive notion*: each part is itself a system with its own service and structure, down to a lowest decomposition level of real-world objects/materials.
- **Service.** The externally observable behaviour a system offers to its environment; the primary thing the designer first conceives ("design the service" is design step one).
- **The interaction-system concept vocabulary.** A layered set of elementary design concepts used to specify behaviour precisely: **entity**, **interaction point**, **interaction**, **behaviour**, **action**, and the **causality relation** (a causality condition + attribute constraints + a probability constraint defining when/whether a result action occurs). Behaviours are assigned to entities; entities can be (de)composed at action points.
- **Protocol vs. service.** A *service* specifies what an interaction system provides; a *protocol* specifies how distributed entities cooperate to realize that service (protocol data units, protocol elements, refined protocol-entity structure). The book gives generally applicable methods for structuring both.
- **Reference model.** A structure of related, standard interaction systems (the ISO-OSI reference model is the worked example) that can be manipulated and reused as a frame for many concrete designs.

### From Lauber (complex-style selection and microservice identification)
- **Complex architectural style.** A style that decomposes a system into multiple decentralized components orchestrated or choreographed into a cohesive whole - chiefly SOA and microservices (MSA). Monolithic architecture is "not complex" in this sense but is retained as the baseline for comparison.
- **Quality criteria.** The decision factors that should drive style selection (business and organizational requirements as well as technical qualities). Lauber's first literature review catalogs the quality criteria most emphasized for SOA vs. MSA and identifies which criteria most strongly *differentiate* the two.
- **Microservice identification.** The activity of partitioning a system into microservice candidates. Lauber characterizes identification methods by their constituent **activities, artifacts, and techniques**, and by their **initial environment** (greenfield vs. brownfield).
- **Initial environment.** Greenfield (building new, no legacy assets) vs. brownfield (migrating/decomposing an existing system). The available pre-existing assets dictate which identification techniques are feasible and which quality criteria can be measured.

## Frameworks & models

### The Vissers conception/specification method
A top-down design followed by bottom-up construction:

1. **Design the service.** Conceive the externally observable behaviour you want to dispose of.
2. **Decompose into parts.** Imagine a composition of parts, conceive each part's service, and check that the composition actually provides the required service. Search the environment for real-world objects that provide the conceived parts' services.
3. **Repeat (stepwise refinement).** For any part with no available real-world object, recurse - decompose it further - until you reach a lowest decomposition level of existing objects/materials.
4. **Bottom-up construction.** Map conceived sub-…-subparts onto concrete real-world objects, then interconnect upward, following the blueprint defined at the lowest level, until the full system is assembled.

The method is consistently applied at consecutive **abstraction levels**, with each level expressed in the design language so it can be validated before refining further. Generally applicable structuring methods are provided for services (Chapter 8) and protocols (Chapter 10), with the ISO-OSI reference model as the integrating example. (Vissers et al., 2016)

### Purposes of modelling (a checklist)
A design model serves to: conceive/imagine properties; represent and preserve them; communicate the design among designers; analyze/validate against criteria (correctness, effectiveness, performance, ease of use, cost); seed redesign; seed continued design via decomposition; provide the basis for building the real system; and provide the basis for testing the built system against the conceived design. Related specialized models: test model, validation model, simulation model, blueprint. (Vissers et al., 2016)

### Lauber's quality-driven style-selection framework (SOA vs. MSA)
A systematic literature review structured around research questions: (RQ1) which quality criteria are presented for SOA and MSA, and which are most emphasized for each; (RQ2) how SOA and MSA are compared in existing studies; (RQ3) which quality criteria most differentiate SOA from MSA; (RQ4) how the two compare across their quality criteria, yielding guidelines for *when each style is most appropriately used*. Monolithic architecture is included as a comparison baseline. The output is a comparative, criteria-based basis for architectural decision-making rather than a predetermined "choose microservices." (Lauber, 2024)

### Lauber's microservice-identification taxonomy and framework
A taxonomy of identification approaches ordered along the **initial-environment** dimension (greenfield/brownfield), classifying each approach by its activities, artifacts, techniques, degree of automation, and system representation. Key empirical findings:
- **Degree of automation correlates with greenfield and with graph-based representations.** Greenfield approaches are highly likely (85–100%) to be fully automated; over 85% of *automatic* methods use graph-based system models, versus ~54% of manual and ~44% of semi-automatic methods.
- **Technique families.** Clustering dominates: hierarchical clustering is the single most prominent technique, alongside centroid-based (k-means and variants) and density-based (DBSCAN) clustering. Genetic algorithms are applied exclusively in fully automated processes. The application of *guidelines* (rule-based, less formalized partitioning) ranks as commonly as genetic algorithms and never reaches full automation, though it can support decisions through technical visualizations.
- **Data-collection methods (brownfield).** Model-based analysis (most used for automated identification), static analysis, and dynamic analysis, with automation likelihood varying by method. (Lauber, 2024)

## Patterns
The reusable design constructs emphasized by these sources:

- **Service/Protocol layering** - *Intent:* separate *what* an interaction system provides (service) from *how* cooperating distributed entities realize it (protocol), specified independently and then related. *When to use:* designing distributed/interactive systems where you must reason about externally observable behaviour separately from internal cooperation. *Trade-offs:* strong rigor, analyzability, and standardization (the OSI model) at the cost of formalism that a quick prototype does not need. (Vissers et al., 2016)
- **Reference model** - *Intent:* a structure of related standard interaction systems reused as a frame for many concrete designs. *When to use:* a domain with recurring layered interactions (networking/communications, platform stacks). *Trade-offs:* powerful reuse and shared vocabulary; can be over-general for a single small system. (Vissers et al., 2016)
- **Service-Oriented Architecture (SOA)** - *Intent:* compose applications from loosely coupled, interoperable, reusable services, typically with shared governance/middleware. *When to use:* enterprise integration across heterogeneous systems where reuse and interoperability dominate. *Trade-offs:* strong reuse/interoperability; heavier middleware and governance; coarser granularity than microservices. The closest ancestor of MSA. (Lauber, 2024)
- **Microservice Architecture (MSA)** - *Intent:* fine-grained, independently deployable, decentralized services with isolated data. *When to use:* when independent deployment/scaling and team autonomy outweigh distributed-systems cost, and when the right quality criteria (per Lauber's comparison) favor MSA over SOA. *Trade-offs:* agility, scalability, fault isolation vs. operational and consistency complexity; identification (drawing the boundaries) is itself a hard, technique-dependent task. (Lauber, 2024)

## Principles & best practices

- **Externalize the design into a model expressed in an appropriate design language.** Beyond elementary complexity, you cannot reliably hold a design in your mind; use a model that meets preciseness, parsimony, unambiguity, and clarity. (Vissers et al., 2016)
- **Make abstraction explicit.** State which aspects are essential for the model's purpose and discard the rest; including irrelevant properties only breeds confusion and error. (Vissers et al., 2016)
- **Design the service before the structure.** Conceive externally observable behaviour first, then decompose; check at each step that the composition actually delivers the intended service. (Vissers et al., 2016)
- **Use stepwise refinement across abstraction levels.** Decompose top-down to a lowest level of existing objects, then construct bottom-up against that blueprint. (Vissers et al., 2016)
- **Choose architectural styles by quality criteria, comparatively.** Do not predetermine the style; assess SOA vs. MSA (vs. monolith baseline) against the criteria that matter to the business and organization. (Lauber, 2024)
- **Integrate organizational, operational, and technical perspectives.** Style selection and microservice migration fail when only the technical view is considered; cover the whole modernization process. (Lauber, 2024)
- **Match the identification technique to the initial environment and available assets.** Greenfield favors automated, graph-based clustering; brownfield's feasible techniques depend on which assets (models, source, runtime traces) exist and which quality criteria can actually be measured. (Lauber, 2024)

## Anti-patterns & pitfalls

- **Specifying designs in natural language.** Designs described in natural language "normally suffer a great deal of ambiguity"; use an artificial design language. (Vissers et al., 2016)
- **Confusing a property with its expression.** The model is a window onto properties, not the properties themselves; conflating them leads to misinterpretation. (Vissers et al., 2016)
- **Over- or under-specifying the model.** Violating parsimony (too much) or preciseness (too little) both undermine the model's purpose. (Vissers et al., 2016)
- **Predetermining the architectural style.** Much existing research and practice picks a style without comparative assessment, hindering informed decisions. (Lauber, 2024)
- **Ignoring the organizational/operational dimensions of microservice adoption.** A common gap; modernization "covering the entire process" is needed, not just code-level decomposition. (Lauber, 2024)
- **Applying an identification technique that the initial environment cannot support.** E.g., expecting fully automated greenfield-style clustering on a brownfield system whose only asset is unstructured representations. (Lauber, 2024)

## Decision guidance
How an agent should reason about conception, specification, and complex-style choice:

1. **For any non-trivial design, build an explicit model in a design language** that satisfies preciseness, parsimony, unambiguity, and clarity; name your abstraction (what is essential) up front. (Vissers et al., 2016)
2. **Separate service from structure (and protocol).** Specify externally observable behaviour first; then decompose and define how parts cooperate. Recurse via stepwise refinement; validate at each abstraction level before going deeper. (Vissers et al., 2016)
3. **When choosing among complex styles, run a quality-criteria comparison** of monolith vs. SOA vs. MSA against the business/organizational/technical criteria that dominate this system, rather than defaulting to a fashionable style. (Lauber, 2024)
4. **If microservices win, plan identification by environment.** Greenfield → lean toward automated graph-based clustering (hierarchical/centroid/density) and/or genetic algorithms. Brownfield → choose model-based, static, or dynamic analysis according to available assets; expect guideline-based, semi-automated partitioning where full automation is infeasible. (Lauber, 2024)
5. **Keep the whole modernization process in view** - organizational, operational, and technical - not just the decomposition step. (Lauber, 2024)

## Source attribution

- **Vissers, Ferreira Pires, Quartel & van Sinderen (2016), *Architectural Design: Conception and Specification of Interactive Systems.*** Design-model theory, the four model requirements, the precise definition of abstraction, design language, external/internal system perspectives, the recursive system notion, the entity/interaction/behaviour/action/causality vocabulary, service vs. protocol structuring, reference models (ISO-OSI), the top-down design / bottom-up construction method, and purposes of modelling. Informs Overview, Core concepts (design theory), Frameworks & models (method, purposes), Patterns (service/protocol layering, reference model), and the conception/specification principles, anti-patterns, and guidance.
- **Lauber (2024), *Designing Complex Software Architecture: Guidelines on Pattern Selection and Microservice Identification Methods.*** The SOA-vs-MSA quality-criteria literature review and research questions, the comparative style-selection framework, the microservice-identification taxonomy/framework (greenfield vs. brownfield; activities/artifacts/techniques), and the empirical findings on automation, clustering/genetic techniques, and data-collection methods. Informs Overview, Core concepts (complex-style selection), Frameworks & models (selection framework, identification taxonomy), Patterns (SOA, MSA), and the selection/identification principles, anti-patterns, and guidance.

## Recent developments (2024–2026)

- **LLMs are now a major microservice-identification technique.** Lauber's taxonomy (clustering, genetic algorithms, guidelines, by greenfield/brownfield) has been extended in practice by LLM-assisted decomposition that combines code understanding with the established graph-clustering methods. A 2025 pattern-driven, LLM-assisted approach reported 84% precision and 65% recall on modular decomposition, and contrastive-learning work (MonoEmbed) improves LLM representations specifically for monolith-to-microservice boundary detection. This adds a new technique family to the identification framework while keeping its greenfield/brownfield and automation dimensions intact. (Springer, 2025, https://link.springer.com/chapter/10.1007/978-981-95-5012-8_16; ResearchGate, "MonoEmbed," 2025, https://www.researchgate.net/publication/397384208_MonoEmbed)

- **AI-assisted decomposition emphasizes the brownfield case.** New tooling and research target the harder brownfield environment Lauber flags, using static, dynamic, and model-based analysis plus AI risk-scoring to choose low-risk Strangler Fig entry points, reducing identification effort "from weeks to hours." The whole-process caution (organizational and operational dimensions, not just code-level decomposition) remains central. (Augment Code, 2025, https://www.augmentcode.com/guides/15-ai-driven-tactics-to-speed-monolith-to-microservices-migration; ResearchGate, "AI-Assisted Legacy Modernization," 2025, https://www.researchgate.net/publication/392502744)

- **Style-selection caution validated by the consolidation trend.** Lauber's argument against predetermining a style and for comparative, quality-criteria-driven choice is reinforced by the 2025 wave of organizations consolidating microservices back into modular monoliths after underweighting operational and consistency criteria. (SoftwareSeni, "From Microservices Consolidation to Modular Monoliths," 2025, https://www.softwareseni.com/understanding-modern-software-architecture-from-microservices-consolidation-to-modular-monoliths/)

- **Specification rigor meets AI agents.** The Vissers emphasis on externalizing designs into precise, unambiguous design languages aligns with the 2025 "Architecture as Code" move to give AI coding agents machine-checkable specifications rather than ambiguous natural-language prose, an old academic principle finding fresh practical motivation. (DDD Academy, "Architecture as Code," 2025, https://ddd.academy/architecture-as-code/)

- **Foundations stable.** The Vissers design-model theory (preciseness/parsimony/unambiguity/clarity, abstraction, service vs. protocol, stepwise refinement) and Lauber's selection-and-identification frameworks are unchanged; the development is AI-assisted identification techniques and renewed relevance of precise specification.
