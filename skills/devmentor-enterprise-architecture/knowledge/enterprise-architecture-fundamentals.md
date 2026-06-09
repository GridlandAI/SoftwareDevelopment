# Enterprise Architecture Fundamentals

**Domain:** 12 - Enterprise and Business Architecture
**Primary sources:** Fundamentals of Enterprise Architecture (Tanusree McCabe, 2024); The Concept of Enterprise Architecture from Theory to Practice (Tiko Iyamu, 2024); The Survivor's Guide to Enterprise Architecture (Gray, Sykes & Homan, 2025)

## Overview

Enterprise architecture (EA) is the discipline of designing and governing change across an entire organization so that technology investment connects coherently to business strategy. Where software architecture solves the problem of building one well-designed system, EA solves the *enterprise-wide* problems that cut across many systems, teams, and business units: convergence, reuse, standardization, modernization, and the alignment of business and IT. McCabe frames EA as the practice that "defines the north star" - the strategic direction that guides all technology investment in a way that connects business to technology. Iyamu frames it as a change agent and a blueprint developed, implemented, and used to explain and guide how information systems, information management, and business processes accomplish organizational goals.

EA matters in the SDLC because individual software teams, left to optimize only for their own objectives, produce silos, technology sprawl ("chaos"), and technical debt that drag the whole organization down. EA provides the principles, standards, capability models, and roadmaps that let every delivery team build reusable, cost-effective, secure, scalable software that serves the enterprise rather than just one unit. The recurring theme across all sources is that EA must be *effective* and *grounded*: an EA practice disconnected from real delivery becomes an ivory tower that adds no value and is rightly resented.

## Core concepts

**What EA is (and is not).** EA is enterprise-wide and holistic in scope: its context is the entire organization plus aspects of its interactions with partners and markets. It is not just IT, and it is not just a set of documents. It is the connection between business and IT strategy - done "right," Gray/Sykes/Homan argue, there is no alignment to be done because business architecture and IT architecture are the same thing viewed from different angles. EA spans operational and strategic levels and is iterative and continuous; there is no "end state."

**The value proposition (McCabe).** Strong EA avoids three failure symptoms of weak EA:
- **Silos** - vertical-only decisions where each unit optimizes for itself, producing duplication and disjointed customer experiences. An enterprise perspective stitches experiences together across units.
- **Chaos** - the absence of technology standards, producing sprawl that impedes talent mobility, multiplies the cost of security and governance, and reduces agility. Proactive standards accelerate development; reactive or absent standards cause chaos.
- **Technical debt** - shortsighted, immediate-need decisions that must be remediated later (for example, lift-and-shift cloud migrations that never reap cloud benefits).

The benefits, conversely, are better/faster/cheaper delivery through shared vision, guardrails for safe innovation, and future-proofed, competitive applications.

**Effective vs. ineffective EA (McCabe).** Each benefit has a failure mode at the opposite extreme: avoiding silos can itself become a *siloed ivory tower*; avoiding chaos can become *over-standardization that stifles innovation* (EA as dictator); reducing technical debt can become an *inability to guide risk-based "good enough" decisions*. Effective EA is grounded in real needs, rightsizes its standards, and makes implications of accepted risk clear.

**EA as a change agent (Iyamu).** EA addresses the challenges that come with change in both the computing and business environments. It bridges the gap between strategic planning and implementation, defines the scope, scale, and nature of required change, and ensures uniformity, reuse, and manageability of change from the current state to the strategic intent. To do this it must be flexible, adaptive, and governed by principles.

**Capabilities (McCabe).** A *business capability* describes what the business is able to do, independent of any solution, org unit, role, or technology; it endures even as organizations restructure or technology changes. A *technical capability* describes using technology to produce business outcomes and maps to business capabilities. Technical solutions map to technical capabilities, giving a *lineage from technology to business outcomes*. Capabilities define *what*, never *how*.

**Architecture levels.** Conceptual architecture is the most abstract (concepts and business capabilities); logical architecture concerns functional units and boundaries; physical architecture is the technical implementation. Roles span these levels differently (see Frameworks & models).

## Frameworks & models

### The EA domains (the "four architectures")

Both McCabe and Iyamu organize EA around interrelated, interdependent domains. Iyamu's canonical four (drawn from the Zachman/FEAF tradition):

- **Enterprise Business Architecture (EBA)** - the business viewpoint: vision, mission, strategies, performance metrics, business processes, and relationships. EBA translates strategic plans into operational impact, eliminating the gap between strategy development and tactical decision-making. It is the foundation the other domains build on.
- **Enterprise Information Architecture (EIA) / Data Architecture** - defines the availability and use of information; structures logical and physical data assets and data-management resources; puts information into "containers of chains and classes" that support interoperability and inform application architecture.
- **Enterprise Application Architecture (EAA)** - how software applications and systems are designed and deployed; ensures applications coexist on the same platform without business disruption; provides standards for build/buy/reuse and a framework for migration that considers both functional requirements and investment strategy.
- **Enterprise Technical Architecture (ETA)** - configuration standards, principles, and governance for technology products; drives introduction of new technology based on functional advantage *and* compatibility/interoperability with existing architectures.

Key property: the domains are **interrelated and interdependent** - a change in one (for example, replacing a server in ETA) ripples into the others (the software and IT solutions it hosts in EAA). Domain experts must collaborate so deliverables corroborate.

### EA frameworks (industry methods)

Across the sources the four dominant frameworks are TOGAF, Zachman, FEAF, and Gartner (Iyamu; McCabe references TOGAF and C4).

- **TOGAF (The Open Group Architecture Framework) and the ADM.** Presents EA as a generic methodology; descriptions stress IT competencies. The Architecture Development Method (ADM) is its iterative cycle for developing and governing architecture content. McCabe recommends reusing TOGAF (and the C4 model: Context, Containers, Components, Code) as industry best practice for deliverables. Many organizations use TOGAF to define the *structure* of the architecture while using an agile approach to create the *content* (Gray/Sykes/Homan).
- **Zachman Framework.** First published 1987, revised 1996. A *classification scheme* (not a method) - a 6x6 matrix mapping six interrogatives (who, what, when, where, why, how) against six perspectives (planner, owner, designer, builder, subcontractor, functioning enterprise). It defines architecture as the set of descriptive representations relevant to producing and maintaining an object over its life. Strengths: ease of understanding, addresses the enterprise in its entirety, communicates complex concepts precisely, tool/method independent, suited to complex systems; it does not prescribe sequence or process but ensures all views are captured and ownership is structured. It is widely held to be the most popular framework by longevity and academic attention. Weaknesses: it has too many models with too much depth to ever complete, lacks a specific modeling instrument, has no explicit compliance rules or life-history concept, and is hard to implement in practice - challenges rooted in lack of know-how.
- **FEAF (Federal Enterprise Architecture Framework).** Uses reference models to facilitate communication between stakeholders; common in government contexts.
- **Gartner EA process.** Focuses on transforming business requirements and vision into strategies, and on planning the migration from current to desired future state.

Iyamu's caution: frameworks share common goals but differ in scope and definition; many organizations rely on external consultants for the know-how. Lack of understanding and lack of know-how are the two persistent reasons EA initiatives stall.

### Operating models (Ross/Weill - via Gray/Sykes/Homan)

From *Enterprise Architecture as Strategy* (2006): a two-by-two matrix plotting **business process standardization** against **business process integration** yields four operating models (Diversification, Coordination, Replication, Unification). The highest-level architectural choice is the *type of business model* the architecture is designed to support; under each operating model the EA (principles, business structures, applications, data, governance) differs. Positioning architecture choices against a clearly defined business model is more powerful than "aligning" IT to business after the fact.

Related framing metaphors the sources offer for describing EA: city planning / gardening (planned layout with organic growth and seasons), chess (Wierda - enabling strategic change with no end-state; the best choice keeps future options open), story (Graves - narrative-driven), systems thinking (interconnectedness, feedback loops, emergence, patterns over events), connected web/graph (relationships carry the value), patterns, and coherence (the "mortar" that makes federated pieces fit together).

### The Architectural Strategic Process (ASP - Iyamu)

A four-step iterative approach to enforce EA practice: **Current State** (inventory existing business/technical artifacts) -> **Requirements** (gather business and IT architectural requirements and alignments) -> **Governance** (policies, principles, standards guiding build/buy, deployment, compliance) -> **Desired State** (validated against current state via gap analysis). Each domain applies the ASP; each component defines its own standards and templates; validations move from one step to the next.

### Architect roles and organizational models (McCabe)

Three core roles, differentiated by abstraction level, scope, and volume of decisions:
- **Enterprise architect** - strategic, enterprise-impactful decisions (technology standards, architecture patterns) on a 3-5 year horizon; fewest in number; operates conceptually; solves "how do we converge identity across the enterprise?"
- **Solution architect** - marries capabilities to solutions within an architectural *domain*; bridges business and technology; often uses DDD; mid-number; solves "product, platform, or service?"
- **Application architect** - technical design of a specific system/application; most numerous; increasingly merged with tech-lead/senior-engineer; solves "microservices or not?", "four nines availability?"

Common traits: trust, expertise, problem-solving, leadership/influence (more than positional authority). Specialized variants exist for security, network, cloud, site reliability, and data. Senior instantiations: Enterprise Chief Architect (ECA, reports to CIO/CTO) and Divisional Chief Architect (DCA).

Three EA *functions* (distinct from roles): **Strategy** (defines what to do - strategies, standards, policies, principles, processes), **Enablement** (operationalizes via tools and training), **Oversight** (enforces via governance, risk, and compliance controls).

Organizational models: **Centralized** (dedicated central team; preserves objectivity and consistency but is further from delivery and less scalable), **Federated** (architects embedded part-time in delivery teams; scalable and empowering but risks lost objectivity and duplicated, siloed decisions), and **Hybrid** (usually the best fit - centralize EA strategy/enablement/oversight, federate application architects, mix solution architects). Any model requires partnership with accountable technology, business, and product leads.

## Patterns

- **EA domain decomposition (EBA/EIA/EAA/ETA)** - Intent: structure the enterprise into four interdependent architecture domains so business, information, application, and technology concerns each get distinct deliverables, methods, and owners while staying interrelated. When to use: organizing any enterprise-scope architecture program. Trade-offs: clear separation of concerns and ownership vs. coordination overhead and ripple effects across domains.
- **Layered architectural domain model with capability mapping** - Intent: group capabilities into architecture domains arranged in layers (inspired by presentation/logic/data), tying a business-capability model to a technical-capability model so technology traces to business outcomes. When to use: deciding where to invest, converge, or deprecate. Trade-offs: enduring, technology-agnostic view vs. modeling is "more art than science" and needs maintenance.
- **Architecture decision record (ADR)** - Intent: document a recommendation with identifier, description, metadata, stakeholders (RACI), status, date, problem statement, alternatives with trade-offs, rationale, and implications (including debt/risk). When to use: every architecture decision, especially deviations from standards. Trade-offs: documented rationale and governance support vs. discipline to keep records current.
- **Capability target architecture** - Intent: document the shared destination state for a group of capabilities (a domain) with conceptual/logical/physical views, sequence diagrams, and domain-level decisions about invest/deprecate/converge. When to use: solution-architect domain planning. Trade-offs: strategic alignment vs. effort to keep target current as the domain evolves.
- **Reference architecture and patterns-as-code** - Intent: codify proven solutions as documented blueprints, reference implementations, or reusable templates/modules/libraries. When to use: when a solution recurs and reuse would accelerate teams. Trade-offs: acceleration and consistency vs. up-front cost and the need to prove patterns through real implementation, not theory.
- **OKR-based EA strategy** - Intent: define the EA strategy itself with objectives and measurable key results (outcome-based and effort-based), tying architecture work to business value. When to use: establishing or justifying an EA practice. Trade-offs: makes EA value visible vs. requires data sources, ownership, and frequency; measure outcomes, not artifact counts.

## Principles & best practices

- **Connect business to technology; create a shared destination state.** EA's purpose is a "north star" that aligns business, technology, product, and architecture on common goals (McCabe).
- **Capabilities are enduring; solutions are not.** Model what the business and technology can *do*, agnostic of tooling, to provide stable context for decisions (McCabe).
- **Govern with policies, principles, and standards in that derivation order.** Policies (from vision/strategy) lead to principles (the collective will) which guide standards (concrete, reusable building blocks). Principles enable incremental, consistent decision-making (Iyamu).
- **Rightsize standards as guardrails, not handcuffs.** Proactive standards accelerate delivery; over-prescription stifles innovation and breeds rebellion (McCabe).
- **Architecture decisions are trade-offs, not perfection.** Aim for "good enough," document assumptions, and make accepted risk explicit (McCabe).
- **Domains are interdependent - design for ripple effects.** Domain experts must collaborate so deliverables corroborate; a change in one domain affects the others (Iyamu).
- **Bridge the business-IT gap continuously.** This is a primary justification for EA but is constantly eroded by rapid change; it requires shared vision, business/technical requirements, process orientation, and skillsets (Iyamu).
- **Measure impact, not volume.** Counting standards, artifacts, or compliant apps incentivizes the wrong behavior; measure the outcomes the work drives (McCabe).

## Anti-patterns & pitfalls

- **Ivory tower architecture** - EA decisions made without adequate context, abstract and separated from execution reality, adding no business value (McCabe).
- **Over-standardization / "EA as dictator"** - so much prescription that innovation is stifled and teams rebel against architecture as a bottleneck (McCabe).
- **Inability to guide risk-based decisions** - failing to define "good enough" recommendations or make implications of accepted risk clear (McCabe).
- **Starting EA in the middle.** Launching "digital transformation" or "business transformation" without first identifying the enterprise and laying foundations is a common reason EA initiatives fail (Graves & Beard).
- **Lack of know-how and understanding** - the persistent root cause of slow or failed EA and framework implementation; the framework and the organization's activities must both be understood to choose and apply a framework well (Iyamu).
- **Intangible-value trap** - because many EA benefits are intangible and realized indirectly through domains and projects, business cases are hard to articulate and EA gets dismissed (Iyamu; McCabe's argument for measurability addresses this).
- **Skillset shortage, especially in business architecture** - many organizations lack enough skilled architects; retention of specialists is costly, leaving changes unimplementable (Iyamu).

## Decision guidance

- **Choosing an organizational model:** weigh objectivity vs. proximity to delivery and scalability. Default to **hybrid** - centralize EA strategy/enablement/oversight for consistency and objectivity, federate application architects for scale and empowerment, and decide solution-architect placement by domain impact. Adjust for company size, culture, and engagement model (McCabe).
- **Choosing a framework:** match the framework to the organization's context and maturity, not fashion. Zachman suits classification and completeness of views in complex enterprises; TOGAF/ADM suits a generic, process-driven methodology (often paired with agile content creation); FEAF suits reference-model communication (government); Gartner suits current-to-future migration planning. Expect to need know-how (internal or consultant) and to map the framework onto your actual artifacts and capabilities (Iyamu).
- **Scoping decisions:** the more painful a decision will be to reverse after implementation, the more context you should gather before making it; for time-sensitive decisions, prioritize the most impactful contextual factors rather than waiting for complete information (McCabe).
- **EA vs. software architecture:** use EA for problems that transcend domains/divisions (convergence, standards, modernization strategy); use solution/application architecture for domain- and system-level design. The roles form a continuum from conceptual (enterprise) to physical (application), with overlapping concerns and shared traits.
- **Justifying EA:** frame value as the *business outcomes* the architecture drives, expressed as measurable OKRs/KPIs with clear data sources, ownership, and (typically quarterly) frequency - not as document counts (McCabe).

## Source attribution

- *Value proposition, silos/chaos/technical debt, effective vs. ineffective EA, vision/mission, EA functions, architect roles and specializations, organizational models (centralized/federated/hybrid), deliverables (ADR, architecture pattern, capability/application target architecture), capabilities and the layered architectural domain model, OKR-based strategy, assessment framework vs. maturity model:* Fundamentals of Enterprise Architecture - Tanusree McCabe (O'Reilly, 2024), Chapters 1, 2, 8, 12.
- *EA as change agent and blueprint, the four EA domains (EBA/EIA/EAA/ETA) and their interdependence, governance (policies/principles/standards), the Architectural Strategic Process, bridging the business-IT gap, the Zachman Framework, comparison of TOGAF/FEAF/Gartner/Zachman, EA deliverables, culture/skillset implications:* The Concept of Enterprise Architecture from Theory to Practice - Tiko Iyamu (CRC/Auerbach, 2024), Chapters 3, 10.
- *Definitions of EA and framing metaphors (city/gardening, chess, story, systems thinking, web, patterns, coherence), operating models from Enterprise Architecture as Strategy, "architected approach," frameworks like TOGAF/FEAF/BIAN/ITIL in context:* The Survivor's Guide to Enterprise Architecture - Gray, Sykes & Homan (BCS, 2025), Chapters 3-4.

## Recent developments (2024-2026)

- **TOGAF Standard, 10th Edition kept current and folded into a digital-standards portfolio.** The Open Group issued a 2025 update to the 10th Edition: the standard is now authored in AsciiDoc in a Git-based repository (enabling streamlined collaboration, versioning, and multi-format publishing), gained an improved HTML Digital Edition with better search, and was added to The Open Group Portfolio of Digital Open Standards alongside the IT4IT Standard, ArchiMate, the Open Agile Architecture Standard, and the Digital Practitioner Body of Knowledge. New Series Guides include Architecture Roles and Skills (2024) and An Approach to Selecting Building Blocks (2024); TOGAF certifications passed 150,000 across 171 countries. (The Open Group Blog, July 2025: https://blog.opengroup.org/2025/07/01/navigating-the-togaf-standard-10th-edition-updates-and-insights/ ; https://www.opengroup.org/togaf/new-version)
- **AI-augmented enterprise architecture / the "augmented architect."** A dominant 2025 trend reframes the EA repository as continuously updated and context-aware: agentic AI proactively monitors repository patterns, flags inconsistencies, and suggests corrective actions, while non-architects query EA knowledge through conversational interfaces instead of static documents. EA tools increasingly automate data validation, capability mapping, and artifact creation. (ACL Digital, 2025: https://www.acldigital.com/blogs/how-ai-augmented-enterprise-architecture-is-redefining-business-agility-in-2025-and-beyond ; Go-TOGAF: https://www.go-togaf.com/how-ai-is-revolutionizing-enterprise-architecture-trends-tools-and-tactics/)
- **AI agents entering enterprise applications; the architect's remit expands.** Gartner predicts 40% of enterprise applications will feature task-specific AI agents by end of 2026, up from under 5% in 2025, naming AI agents and AI-ready data as the fastest-advancing technologies on its 2025 Hype Cycle. This pushes EA toward governing context layers (semantics and metadata as the "central brain" for agents), trust, and AI literacy. (Gartner, Aug 2025: https://www.gartner.com/en/newsroom/press-releases/2025-08-26-gartner-predicts-40-percent-of-enterprise-apps-will-feature-task-specific-ai-agents-by-2026-up-from-less-than-5-percent-in-2025)
- **A new "AI Architect" role and adjacent specializations.** Gartner argues the emergence of AI in EA demands a dedicated AI Architect role, and forecasts new hybrid roles (AI model steward, prompt designer, AI auditor, AI product manager) blending business, ethical, and technical skill. This extends, rather than replaces, the enterprise/solution/application architect continuum in the source material. (Gartner: https://www.gartner.com/en/documents/5733283)
- **GenAI expectations recalibrating.** Generative AI entered the Trough of Disillusionment on Gartner's 2025 AI Hype Cycle as organizations moved from pilots toward business-aligned implementations and confronted its limits, reinforcing McCabe's "rightsize, measure impact not volume, good enough" guidance for governing AI-related architecture decisions. (Gartner, Aug 2025: https://www.gartner.com/en/newsroom/press-releases/2025-08-05-gartner-hype-cycle-identifies-top-ai-innovations-in-2025)
- **Framework fundamentals stable.** TOGAF/ADM, Zachman, FEAF, the four EA domains, operating models, and architect roles in the source material remain the working vocabulary of the field; the 2024-2026 shift is the layering of AI assistance and AI governance on top of them, not their replacement.
