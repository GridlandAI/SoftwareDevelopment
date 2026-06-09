# Business Architecture

**Domain:** 12 - Enterprise and Business Architecture
**Primary sources:** Fundamentals of Business Architecture Modeling (Řepa & Svatoš, 2024); Mastering Enterprise Solution Modeling: A Guide to Agile (Gu, 2024)

## Overview
Business architecture is the discipline of describing an enterprise as a designed system so that its structure, processes, capabilities, information, and organization can be aligned with business objectives and with the technology that supports them. It produces a holistic, multidimensional blueprint of the enterprise: capabilities, end-to-end value delivery, information, and organizational structure, along with how these relate to strategy, products, policies, initiatives, and stakeholders. Its central purpose is to bridge the gap between strategy and execution, and to give executives, business managers, IT professionals, and other decision makers a common, unambiguous language for the business.

Why it matters in the SDLC: software is rarely built in a vacuum. A system that is internally well engineered but misaligned with the business it serves is a failed system. Business architecture supplies the conceptual layer above requirements and design, ensuring that what gets built corresponds to how the enterprise actually operates and intends to operate. When done with a systematic method rather than an intuitive "how colleagues did it before" approach, the resulting models are reusable, sustainable, readable, clear, and internally consistent (Řepa & Svatoš, 2024). Two complementary stances appear in the source material: a rigorous, ontology-grounded modeling method for capturing the business system (MMABP), and an agile, lean bridge between enterprise architecture and solution architecture for large solutions (A-ESA).

## Core concepts

**Business system.** Any system created and continuously developed by people to achieve specific business goals. It comprises a set of mutually collaborating business processes working toward business objectives, constrained by the rules and regulations of the environment (business rules). The information system is treated as an integral component of the business system and, importantly, as itself a model of that business system (Řepa & Svatoš, 2024).

**Intentionality and causality.** The two fundamental phenomena MMABP says any business model must hold in equilibrium. Intentionality is the goal-directed side: goals achieved through actions organized into processes, driven by events. Causality is the structural side: objects (people, entities, values, meanings) interconnected by rules, conditions, constraints, and dependencies that give the collection logical coherence. A well-calibrated business system is an equilibrium between the two.

**Being vs. behavior; system vs. particular view.** MMABP's philosophical framework crosses two phenomena (being = the world's existing and possible states; behavior = events caused by actors' actions) with two perspectives (system view = the whole, static; particular/temporal view = a single element over time). This yields four fundamental model types and, in practice, four diagrams (see Frameworks & models).

**Capabilities.** What the enterprise is able to do, expressed independently of how or who. The Business Architecture Guild (BIZBOK) and TOGAF both define business architecture as holistic, multidimensional views of capabilities, end-to-end value delivery, information, and organizational structure. Capability views answer the strategic "what can we do" question and are the natural anchor for capability-based planning. In A-ESA the capability view (and organization view) form the enterprise area, addressed to CxOs, strategy consultants, and enterprise architects (Gu, 2024).

**Value streams / end-to-end value delivery.** The sequence of activities that delivers a result of value to a stakeholder, viewed end to end across functional silos. Standards differ: ARIS offers value chain / value stream maps, BIZBOK emphasizes end-to-end value delivery, and MMABP captures the system of processes in a process map rather than a single canonical value-stream notation.

**Business process.** A coordinated set of activities that achieves a goal. MMABP distinguishes the process map (the system of processes and their collaboration, a static global view) from the process flow model (the algorithmic detail of one process, a temporal view). It treats both as legitimately part of business architecture, rejecting the idea that architecture must stop at the strategic level.

**Information / data (model of concepts).** The structure of the real-world objects the business works with. MMABP calls its conceptual data model the model of concepts and models objects in an object-oriented way (attributes plus life-cycle operations), not as pure data. The object life cycle model then captures the causal evolution of each object through its states.

**Organization.** How people and units are arranged to perform the work. Three classic organizational perspectives are the hierarchical (operational, management, executive levels), the functional (marketing, operations, HR, finance, accounting), and the process perspective, which integrates across the other two (relevant context from Shishkov, 2019, and reflected in BIZBOK/TOGAF organization views).

**Business-IT alignment.** The persistent goal of keeping what IT builds in correspondence with the business. TOGAF makes business architecture one of four architectures (with data, application, technology); A-ESA explicitly positions itself as the link between enterprise architecture and solution architecture so that the two do not drift apart.

## Frameworks & models

**MMABP minimal business architecture (Řepa & Svatoš, 2024).** A methodology that captures a business system with the fewest possible diagrams and symbols while staying complete, mutually consistent, understandable to ordinary users, and stable against common change. It maps four model types onto four standard diagrams:
- *Model of concepts* (UML Class Diagram) - global ontological/static view of objects and relationships (real-world modality, modal logic).
- *Object life cycle model* (UML State Machine Diagram) - detailed ontological/temporal view of one object's states and transitions (real-world causality, causal logic).
- *Process map* (TOGAF Event Diagram) - global behavioral/static view of the system of processes and their collaboration toward goals.
- *Process flow model* (BPMN) - detailed behavioral/temporal view of the algorithmic flow of a single process under all circumstances.
How to apply: develop the four views iteratively for the part of the business in scope, then enforce the consistency rules so they form one coherent model. Use it when you need a rigorous, reusable, incrementally extensible model and want to avoid drowning in the diagram catalogs of full TOGAF/ArchiMate.

**Three foundational principles of MMABP.** Modeling (the organization is a model of the real world and must be derived from realities outside it), abstraction (build hierarchical abstract concepts), and three architectures (descend from a clean conceptual model of the real world, then add generic technological/organizational aspects, then individual implementation specifics). MMABP models in this book sit mostly at the conceptual level.

**Four levels of process abstraction (Řepa & Svatoš, 2024).** Process map at the top, then process flow detail, descending toward executable/technology-level process models. The book shows how a conceptual-level process flow must be transformed when moved to an executable form, and stresses respecting the natural parallelism of processes rather than forcing artificial sequence.

**A-ESA / S3 model (Gu, 2024).** Agile Enterprise Solution Architecture: a lean intermediary between EA and solution architecture for large, complex enterprise solutions. "S3" = simple (a minimal, just-enough, coarse-grained set of model elements), significant (focus on significant case scenarios and key NFRs, not a full architecture), and systematic (an IT-service-based liaison integrating EA, BA, AA, DA, TA). It is organized into areas (enterprise capability, case scenario, outline overview, functional, operational, validation), views (capability, organization, use-case/process flow, architecture outline, service interaction, runtime operational, validation, etc.), elements, and properties. Apply it when a solution is large enough to need architecture but big-bang EA would over-architect; skip it entirely when a solution is small, short-run, or "fits in your head."

**A-ESA thinking framework (Gu, 2024).** Treats architecture as art (guidelines, techniques, tradeoff balancing) plus science (structures and relationships). It pushes architects to think in terms of NFRs/quality-attribute requirements (the model is treated as the NFR authority), significant case-scenario walkthroughs that reduce complexity, value-versus-cost reasoning (value = benefit - cost, with cost as a first-class constraint), and architectural measurement that maps metrics back to model areas.

**Capability-based planning.** Use capabilities as the stable unit for planning investment and change: identify what the enterprise must be able to do, assess current vs. target maturity, and prioritize initiatives by their contribution to strategic capabilities rather than by org chart or technology. BIZBOK supplies a Business Architecture Maturity Model; capabilities give a change-resistant anchor because they shift more slowly than processes or systems.

## Patterns

**Minimal complete model set** - Intent: capture a business system with the smallest set of mutually consistent views that is still complete. When to use: any business analysis where you want reusability and resistance to over-documentation. Trade-offs: requires discipline and a CASE tool to maintain cross-view consistency; offers less notational richness than full TOGAF/ArchiMate. (Řepa & Svatoš)

**Four-view consistency (object + process, global + detailed)** - Intent: model the same reality from being/behavior and system/particular perspectives so the views cross-check each other. When to use: when correctness matters and you cannot afford simulation; consistency rules substitute for executable validation. Trade-offs: maintaining the consistency rules by hand is error-prone without tooling. (Řepa & Svatoš)

**Process map before process detail** - Intent: model the system of processes and their collaboration before diving into any single process's flow. When to use: to keep strategic and process-management views in sync and avoid local optimization. Trade-offs: process maps are not standardized across BPMN/UML, so notation must be chosen deliberately. (Řepa & Svatoš)

**Object as attributes + life cycle** - Intent: model business objects object-orientedly (state and behavior), not as flat data. When to use: when causality and temporal rules of the domain matter, e.g., order, shipment, contract life cycles. Trade-offs: more analysis effort than pure data modeling; needs OntoUML-influenced concepts kept lightweight enough for ordinary analysts. (Řepa & Svatoš)

**Significant case scenario walkthrough** - Intent: drive architecture from a few significant scenarios rather than enumerating all requirements. When to use: large solutions where exhaustive modeling is infeasible; surfaces the NFRs and cross-cutting concerns that actually shape the architecture. Trade-offs: depends on choosing genuinely representative scenarios; blind spots if the wrong ones are chosen. (Gu)

**EA-to-SA bridge (single source of truth)** - Intent: maintain one lean model under an ESA architect that links enterprise concerns to solution design so they do not diverge. When to use: programs spanning strategy, business, application, data, and technology layers. Trade-offs: requires a capable, T-shaped architect and tool support; the model's value collapses if it is allowed to go stale. (Gu)

## Principles & best practices
- Prefer systematic analysis on general principles over intuitive patchwork; it yields reusability, sustainability, readability, clarity, and consistency (Řepa & Svatoš).
- Keep designed processes in harmony with the business ontology; respect the natural parallelism of business processes instead of forcing artificial sequence.
- Aim for minimal but complete: as few diagrams and elements as possible while staying complete and consistent. More communication, not more documentation (both sources).
- Treat the four MMABP views as complementary windows on one reality and enforce factual, temporal, and structural consistency rules between them.
- Model objects as a unity of attributes and life-cycle operations; capture causality, not just data structure.
- Distinguish the conceptual (real-world) level from the technology/implementation level and move between them deliberately.
- Incorporate non-functional requirements into the architecture model as early as possible; treat the model as the NFR authority (Gu).
- Tailor depth to need: don't build an ESA (or even an SA) for a small, short-run, obvious solution; conversely, don't let a large solution drift without an architectural bridge (Gu).
- Target each view at a specific audience; never show arcane technical detail to business users or business abstractions to technical specialists (Gu).
- Reason in value vs. cost terms; cost is a legitimate first-class architectural constraint (Gu).

## Anti-patterns & pitfalls
- Intuitive, "how it was done before" analysis that produces inconsistent, non-reusable, hard-to-read models (Řepa & Svatoš).
- Pure data analysis: modeling business objects as flat attributes and losing their causal life cycles.
- Over-architecting: applying heavyweight enterprise or solution architecture to a small or obvious problem (Gu).
- Treating business architecture as purely strategic and stopping above process detail, when ontology-based approaches show process detail can and should be part of the architecture.
- Letting strategic process maps and detailed process models drift out of mutual consistency.
- Modeling the whole company up front "for completeness" instead of focusing on what is relevant to the problem and extending incrementally.
- Letting the single-source-of-truth model go stale, so it no longer reflects reality and loses authority (Gu).
- Treating the ESA as a purely technical, high-availability concern and missing the broader business implications (Gu).

## Decision guidance
- **Rigor vs. agility.** Choose MMABP when correctness, consistency, and long-term reusability of a business-system model are paramount and you can invest in a CASE tool and disciplined consistency checking. Choose A-ESA when you need a lean, just-enough bridge between EA and a large solution and speed-to-value matters more than exhaustive formal modeling.
- **How much architecture.** Scale effort to solution size and complexity. No ESA/SA for small, short-run, obvious work; a minimal incrementally extended model for medium work; a fuller multi-view architecture for large system-of-systems programs. Increment in an agile fashion rather than big-bang.
- **Which standard to anchor on.** TOGAF/ArchiMate for broad enterprise-architecture context and stakeholder communication at the strategic level; BIZBOK when business architecture is treated as a management discipline with capability maps and maturity models; BPMN for detailed process flow; UML class and state-machine diagrams for the ontological/object views. MMABP integrates these and tells you the minimum set to use.
- **Capabilities vs. processes as planning unit.** Use capabilities for stable, strategy-linked planning (they change slowly); use processes when you need executional detail and performance measurement. Keep the two consistent.
- **Selection heuristic.** Start from the problem, not from a desire to model everything. Pick the smallest set of views that lets you reason about the decision at hand, target each view at its audience, and extend only as new problems demand.

## Source attribution
- Business system, intentionality/causality, being-vs-behavior framework, the four MMABP models and their mapping to UML/BPMN/TOGAF diagrams, three foundational principles, four levels of process abstraction, consistency rules, model of concepts and object life cycle, minimal-architecture philosophy, and the survey of approaches (BIZBOK, TOGAF/ArchiMate, Enterprise Engineering, BPM, OMG standards, DEMO, OntoUML/UFO): *Fundamentals of Business Architecture Modeling* (Řepa & Svatoš, 2024), Chapters 1-6.
- A-ESA / S3 model, areas/views/elements/properties, capability and organization views, EA-to-SA bridge, S3 objectives, the thinking framework, NFR-as-architecture-authority, value-vs-cost reasoning, significant case scenarios, architectural measurement, and over-architecting guidance: *Mastering Enterprise Solution Modeling: A Guide to Agile* (Gu, 2024), Chapters 1-6.
- Organizational perspectives (hierarchical/functional/process) as supporting context: Shishkov (2019), reflected here for the organization building block.

## Recent developments (2024–2026)

- **BIZBOK Guide v14 (March 2025) and a v15 follow-on.** The Business Architecture Guild released BIZBOK Guide v14 with enhanced capability and value mapping (clearer language, usage context, and cross-mapping), refreshed industry reference models (financial services, government, healthcare, insurance, manufacturing, telecom, transportation, mission-driven), and explicitly noted "strong alignments with AI's potential" while reinforcing foundational practice; a v15 was subsequently announced. (Morningstar / The AI Journal, 2025: https://www.morningstar.com/news/pr-newswire/20250326sf50223/business-architecture-guild-releases-bizbok-guide-v14 ; https://aijourn.com/business-architecture-guild-releases-bizbok-guide-v15/)
- **AI-assisted capability and value mapping.** Vendors and practitioners now use LLMs to draft capability maps, value streams, and BIZBOK-to-ArchiMate cross-mappings from prompts and documents, turning the maps from static deliverables into faster-to-produce, queryable artifacts. Capability models are increasingly positioned as the stable anchor for prioritizing AI investment. (Agora Insights, 2025: https://courses.agorainsights.com/pages/blog?p=business-architecture-using-ai-capabilities-tips ; Bizzdesign: https://bizzdesign.com/blog/business-architecture-redefined-mapping-bizbokr-archimater)
- **Business architecture as the framing layer for AI transformation.** A recurring 2025 theme: capability and value maps are used to decide where AI delivers business value and to keep AI initiatives tied to strategy rather than technology fashion, with "dynamic rules-based routing" enabling more agile AI integration. (MEGA community, 2025: https://community.mega.com/t5/Business-IT-Transformation/Enable-artificial-intelligence-using-business-architecture/ba-p/21035 ; buzzanalysis 2025 wrap-up: https://www.buzzanalysis.com/2026/01/business-architecture-analysis-and-ai.html)
- **Stable core, shifting application.** The underlying constructs in the source material (capabilities, value streams, the intentionality/causality and being/behavior distinctions of MMABP, the A-ESA/S3 bridge) remain current; the 2024-2026 change is primarily in tooling and AI assistance for producing and maintaining the models, not in the modeling theory itself.
