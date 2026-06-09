# Requirements Types and Quality

**Domain:** 02 - Requirements & Specifications
**Primary sources:** Software Requirements Essentials (Wiegers & Hokanson, 2023); Software Requirements, 3rd Edition (Wiegers & Beatty, 2013); Requirements Writing for System Engineering (Koelsch, 2016); More About Software Requirements (Wiegers, 2009)

## Overview
Teams deal with many *kinds* of requirements-related knowledge, and confusion results when people lack a shared model of them. This file defines the requirement types (business, user, functional, nonfunctional/quality, data, interface, system, business rules, constraints), describes how they connect, and lays out the characteristics of an excellent requirement and an excellent requirement *set*. It also covers the software requirements specification (SRS) as the container for solution requirements. The unifying idea: developers do not implement business or user requirements directly - they implement **functional requirements** derived from a foundation of well-understood business and user needs, subject to quality attributes and constraints.

## Core concepts

### The requirements information model
Wiegers and Hokanson use a three-level model. Information flows top-down, and the diagonal "origin/influence" arrows all converge on the SRS:

- **Business requirements** (top) - *why* the organization is undertaking the project: business opportunities/objectives, success metrics, product vision, scope. The guiding directives for all downstream work.
- **User requirements** (middle) - tasks or goals a user wishes to accomplish with the solution; expressed as use cases, scenarios, or user stories.
- **Solution requirements** (bottom) - what the product must do/be to satisfy user requirements and achieve business objectives. Solution requirements **encompass functional, nonfunctional (quality), and data requirements**, plus manual operations.

Surrounding these:
- **System requirements** - top-level capabilities of a complex multi-subsystem product (often hardware + software); the origin of derived software solution requirements.
- **External interface requirements** - connections to users, other systems, hardware, and networks.
- **Business rules** - directives that define or restrict operations (policies, regulations, laws, standards); often exist *beyond* any one application and lead to derived functional/data requirements.
- **Constraints** - restrictions on the requirements, design, or implementation.

Not every requirement fits tidily into one category. Debating the label matters less than recognizing the need, analyzing it, recording it appropriately, and building to satisfy it. (Wiegers & Hokanson, 2023, Ch. 1)

### Business requirements in detail
Business requirements explain *why* and *what value*. Core elements:

- **Business objectives** - specific, realistic, **measurable** statements of desired outcomes. Prefer absolute targets over relative percentages with an unstated baseline ("Achieve a market share of at least 45% in Australasia by Oct 1, 2024" beats "increase market share by 20%").
- **Success metrics** - how you judge progress. Many objectives are lagging indicators influenced by outside factors, so use **proxy/surrogate metrics** (new customers, average order size) whose trends are proportional to the real goal.
- **Vision statement** - a high-level strategic target. Moore's keyword template: *For [target customers] / Who [need or opportunity] / The [product name] / Is [type of product] / That [key capabilities, core benefits] / Unlike [current reality/competition] / Our product [key advantages].* Vision (the ultimate intent, perhaps never fully attained) is distinct from project **scope** (what a specific cycle realizes).

Containers: **vision and scope document**, **project charter**, market requirements document, or business case. (Wiegers & Hokanson, 2023, Practice #2)

### User requirements: use cases and user stories
User requirements describe what users need to *do*. Variations on a theme (Alexander & Maiden):

- **Scenario** - a single interactive session; the **normal/main/happy-path** flow plus alternative flows and exceptions.
- **Use case** - organizes related scenarios under a template (name as action verb + goal, e.g., "Submit an Article to a Publication"; actors, preconditions, postconditions, normal flow, alternative flows, exceptions). Use cases facilitate top-down thinking and provide context.
- **User story** - *As a `<type of user>`, I want to `<task>` so that I can `<goal>`.* Intentionally brief; a placeholder for conversation. INVEST qualities: Independent, Negotiable, Valuable, Estimable, Small, Testable.
- **Epic** - a large story or workflow spanning multiple stories/iterations.

Use cases and user stories both feed functional-requirement derivation, early testing, and prioritization. (Wiegers & Hokanson, 2023, Practice #6)

### Functional requirements
A **functional requirement** describes behavior the product exhibits under specified circumstances. Developers implement these. Standard patterns use the keyword **shall**:
- `The <user class> shall be able to <do something>.`
- `The system shall <let the user class do something>.`
- `When <conditions/event>, the system shall <do something>.`

Koelsch enumerates many **types of functional requirements**: business rules, transaction corrections/adjustments/cancellations, administrative functions, authentication, authorization levels, audit tracking, external interfaces, certification requirements, searching/reporting, historical data, archiving, compliance/legal/regulatory, structural, algorithms, database, power, network, infrastructure, and backup/recovery. (Koelsch, 2016, Ch. 4; Wiegers & Hokanson, 2023, Practice #14)

### Nonfunctional / quality attribute requirements
"Nonfunctional requirements" most commonly means **quality attributes** (quality-of-service requirements): not what the product does, but *how well*. Lists run to 50+ attributes. Common ones, split by who cares most:

- **External (user-observed):** availability, installability, integrity, interoperability, performance, reliability, robustness, safety, security, usability.
- **Internal (developer/maintainer):** efficiency, extensibility, maintainability, modifiability, portability, reusability, scalability, verifiability.

Koelsch's nonfunctional categories include architectural, capacity, constraints, documentation, efficiency, and more.

Key facts about quality attributes:
- They **cannot all be optimized simultaneously.** Increasing one can help, hurt, or be neutral to another (more security usually reduces usability; more robustness raises integrity/reliability/security).
- They often **drive architecture** or serve as the **origin of derived functional requirements** (a one-line security policy explodes into dozens of functional requirements about passwords, codes, lockout, etc.).
- Eliciting them requires the right questions; "What are your availability requirements?" yields useless answers. Roxanne Miller's catalog (2,000+ questions across 14 attributes) helps probe systematically.

(Wiegers & Hokanson, 2023, Practice #9; Wiegers & Beatty, 2013; Koelsch, 2016, Ch. 5)

### Data requirements
Data is "the glue that connects all other requirement types." Data requirements define data objects, their composition/attributes, relationships, cardinalities, input/output formats, and governance (quality, access, security, privacy, integrity, retention, archiving, disposal). Captured via conceptual/logical/physical **entity relationship diagrams**, **data dictionaries**, and **data flow diagrams**. (See `business-requirements-and-data.md`.)

### Business rules
A **business rule** is a directive that defines or restricts operations and exists independently of any one system. Categories (von Halle): **terms** (definitions), **facts**, **constraints** (must/may not/may only), **action enablers** (if-then triggers), **inferences** (derived knowledge), **computations**. Rules are the *origin* of derived functional and data requirements; trace requirements back to the rule, point to a single master instance, and store rules as an enterprise asset. (Wiegers & Hokanson, 2023, Practice #16)

## Frameworks & models

### Characteristics of an excellent individual requirement
Wiegers & Beatty / Wiegers & Hokanson:
- **Complete** - no necessary information missing.
- **Correct** - accurately states a real stakeholder need or solution property.
- **Feasible** - implementable within known constraints.
- **Necessary** - documents something a stakeholder really needs.
- **Prioritized** - ranked relative to others.
- **Unambiguous** - conveys only one meaning to all readers (the hardest and most challenging, especially across native languages).
- **Verifiable** - there is a way to demonstrate correct implementation.

Koelsch expands to **16 attributes of a good requirement**: accurate/correct, atomic, complete, concise, consistent, does-not-conflict, does-not-duplicate, independent, prioritized, realistic, traceable, traced-to-a-source, unambiguous, understandable-by-stakeholders, unique, verifiable. He treats **unambiguous** as the single hardest attribute ("a knowledgeable person interprets each requirement only one way") and **atomic** as decomposing each requirement to its lowest logical level.

### Characteristics of an excellent requirement set
Beyond per-requirement quality, the *collection* should be:
- **Complete** - no gaps; trace downward from objectives to user to solution requirements.
- **Consistent** - no conflicts between parent and child or among same-type requirements.
- **Modifiable** - organized/labeled so changes are easy to make and record.
- **Traceable** - logical links back to origins and forward to design, code, tests; requires fine granularity and unique, persistent identifiers.

### Levels of abstraction
Requirements objects sit at different granularities (finest to coarsest): functional requirements / acceptance criteria → user stories → use cases → features → epics. Manage complexity by (1) grouping items at a consistent level and (2) using requirement hierarchies. Avoid mixing a one-line "respond to editing directives by voice" (an entire speech subsystem) in among hundreds of fine-grained statements.

### The Software Requirements Specification (SRS)
The SRS is the primary container for functional and nonfunctional solution requirements (also called BRD, functional specification, requirements document - define names consistently within an organization). A rich SRS template (Wiegers & Beatty) includes: introduction (purpose, conventions, scope), overall description (product perspective, user classes, operating environment, constraints, assumptions/dependencies), **system features** (the largest section - the functional requirements, organized by feature/use case/event/user class/subsystem), external interface requirements, nonfunctional requirements (performance, safety, security, quality attributes), and other requirements (business rules, etc.).

A complex systems project starts with a **System Requirements Specification (SyRS)** describing top-level behaviors, from which software and hardware sub-specs are derived. Government projects often use a **Concept of Operations (CONOPS)** as the stakeholder requirements spec.

A good template doubles as a **checklist**: an empty section ("Limitations and Exclusions") prompts the question "are there any, or have we not had the conversation yet?" Use a **shrink-to-fit** philosophy - start comprehensive, then tailor. (Wiegers & Hokanson, 2023, Practice #15; Wiegers & Beatty, 2013)

### Requirement attributes (metadata)
Per requirement, optionally record: ID (unique, persistent, ideally hierarchical-label-based not position-based), author, origin/source, rationale, priority, status (proposed → approved/rejected → implemented → verified/deferred/deleted), validation method, acceptance criteria, version history, estimate, dependencies. Start with a minimal set people will actually populate. (Wiegers & Hokanson, 2023, Practice #14)

## Patterns

- **`Shall` statement** - *intent:* state functional behavior consistently and testably. *When:* functional requirements, especially in formal/systems contexts. *Trade-offs:* monotonous to read; mixing shall/must/will/should/may breeds ambiguity, so pick one verb and use it consistently.
- **User story (As a / I want / So that)** - *intent:* capture who, what, and why concisely. *When:* agile teams, conversation-driven detail. *Trade-offs:* lacks context and detail until elaborated with acceptance criteria; ensure the `<type of user>` is a human/role that can "want."
- **Planguage (quality attribute spec)** - *intent:* specify quality requirements precisely. *When:* products needing rigorous, measurable nonfunctional requirements. Keywords: Tag, Ambition, Scale, Meter, Goal, Stretch, Wish. *Trade-offs:* more thought and space than "the system shall be available 24/7," but eliminates fuzz.
- **Hierarchical labeling (e.g., `Password.Temporary.Request`)** - *intent:* unique, persistent, meaningful identifiers. *When:* any sizable requirement set. *Trade-offs:* more thought than sequential numbering; survives insertions/deletions, unlike position-based numbering.
- **Vision statement template** - *intent:* align all participants on the product's essence. *When:* project start. *Trade-offs:* writing independently then comparing reveals terminology and goal conflicts.

## Principles & best practices
- Write in short, declarative, **active-voice** sentences; name specific user classes and the specific system; tag each requirement with a unique ID; avoid bullet lists (no way to reference a bullet); avoid combining independent requirements with and/or/but/except/else.
- Recast negatives as positives where possible ("may attend only if exhibiting" rather than "may not attend unless exhibiting"); avoid double/triple negatives.
- Quality attribute specs should be **Precise, Necessary, Measurable, Verifiable, Realistic.** "The system shall be secure" is not a requirement - only a starting point.
- A `TBD` means the requirement is **not complete**; track TBDs with a work-off plan.
- Group functional requirements logically (by feature, use case, event/response, user class, subsystem).
- Store and reuse quality requirements (e.g., a security set) across applications; nurture a reuse culture.

## Anti-patterns & pitfalls
- **Using "business requirement" to mean any requirement from the business.** Reserve it for the *why/value* level; conflating it with requested functionality loses the alignment value.
- **Fuzzy quality attributes** ("system shall be fast/secure/available") - unverifiable and unimplementable.
- **Trying to optimize all quality attributes at once** - physically impossible; you must prioritize and accept trade-offs.
- **Vague, unmeasurable business objectives** - you cannot tell if you achieved them.
- **Mixing abstraction levels** as if epics and functional requirements were the same kind of thing.
- **Position-based requirement numbering** - IDs change when items are inserted/deleted above them, breaking traces.
- **Creatively varying terminology** in a spec (calling the same thing "sample" and "run") - every reader wonders whether they differ.
- **Over-specifying requirement attributes** - long attribute lists nobody populates or uses.

## Decision guidance
- **Which requirement type am I looking at?** If it explains *why/value* → business requirement. If it's a user task/goal → user requirement (use case/story). If it's specific system behavior → functional requirement. If it's "how well" → quality attribute. If it's about data structure/relationships → data requirement. If it constrains the business beyond this app → business rule. If it restricts design/implementation → constraint.
- **Use cases or user stories?** Both work if focus stays on usage. Use cases give richer context and exception handling for interactive systems; stories suit agile slice-by-slice implementation. They can be combined (stories abstracted into use cases or epics).
- **How precise must a quality attribute be?** Scale to impact and risk. Architecture-affecting attributes (availability, scalability, reliability) deserve Planguage-level precision early, because retrofitting is expensive. Feature-local attributes can be acceptance criteria on the relevant story.
- **Which requirement-quality attributes matter most?** Unambiguous and verifiable are foundational (you cannot validate what you cannot pin down or test). Complete, correct, and feasible protect against building the wrong/unbuildable thing. Traceable and modifiable matter most where change and certification are likely.

## Source attribution
- The three-level requirements information model, requirement-type definitions, business/user/functional/data requirement detail, quality attribute treatment, abstraction levels, the SRS template and requirement attributes, and the per-requirement and per-set quality characteristics are from **Software Requirements Essentials** (Wiegers & Hokanson, 2023, Ch. 1, 5, Practices #2, #6, #9, #14, #15, #16) and **Software Requirements, 3rd Edition** (Wiegers & Beatty, 2013).
- The 16 attributes of a good requirement, functional/nonfunctional requirement type catalogs, the atomic and unambiguous emphases, "shall" conventions, negatives handling, and TBD/completeness guidance are from **Requirements Writing for System Engineering** (Koelsch, 2016, Ch. 2, 4, 5).
- The "business requirement" terminology caution and the vagueness-vs-specificity tension (Cosmic Truth #9: the requirements may be vague but the product will be specific) are from **More About Software Requirements** (Wiegers, 2009).

## Recent developments (2024–2026)
The requirement-type model and quality characteristics are stable, mature material. Recent developments standardize requirement quality further (EARS, INCOSE rules) and apply AI to writing and checking requirements; a new quality dimension (AI-readiness) is also emerging.

- **EARS notation and the INCOSE quality rules are now mainstream for high-quality requirements.** EARS (Easy Approach to Requirements Syntax) provides a small set of constrained sentence patterns (ubiquitous, event-driven, state-driven, optional-feature, unwanted-behavior, complex) that reduce ambiguity, complementing the "shall" conventions in this file. The INCOSE Guide to Writing Requirements (V4, July 2023) and its 42 rules plus 15 quality characteristics (necessary, appropriate, unambiguous, complete, singular, feasible, verifiable, and more) are widely treated as the reference standard, closely matching Wiegers' and Koelsch's lists. (INCOSE, 2023, incose.org; Jama Software EARS FAQ, 2024-2025)
- **AI-assisted requirement quality checking at authoring time.** Requirements tools now score statements against INCOSE/EARS and flag ambiguity, passive voice, vagueness, and missing tolerances as the analyst writes (Jama Connect Advisor; IBM DOORS Next Requirements Quality Assistant using Watson). This automates the per-requirement quality gate (unambiguous, verifiable, complete) that this file describes. (Jama Software, 2025-2026; IBM, 2025)
- **LLMs draft and refine requirements, but quality still hinges on human revision.** Systematic reviews (2024-2026) find LLMs can generate functional and nonfunctional requirements and acceptance criteria and help detect ambiguity, yet output quality depends on inputs and prompts and routinely needs human correction, so the excellent-requirement characteristics become a review checklist for AI output, not just for human authors. (Cheng et al., Software: Practice and Experience, Wiley, 2026; arXiv 2409.06741)
- **A new implicit quality goal: AI-readiness.** Spec-driven development (GitHub Spec Kit, Amazon Kiro authoring requirements in EARS) shows that precise, singular, testable requirements now double as effective prompts for AI code generation, raising the practical payoff of the long-standing quality attributes and of structured patterns like Planguage and boilerplates. (Kiro, 2025; GitHub Blog, 2025)
- **Quality attributes for AI-intensive systems gain prominence.** Regulation (EU AI Act, in force August 2024) pushes attributes such as transparency, explainability, human oversight, data quality, and robustness into the nonfunctional requirement set for high-risk AI systems, extending the classic quality-attribute catalog. (European Commission, 2024-2025)
