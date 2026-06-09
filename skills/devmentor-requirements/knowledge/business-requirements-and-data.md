# Business Requirements and Data Architecture

**Domain:** 02 - Requirements & Specifications
**Primary sources:** Business Requirements Gathering and Data Architecture (Owens & Walton, 2023); Software Requirements Essentials (Wiegers & Hokanson, 2023); Software Requirements, 3rd Edition (Wiegers & Beatty, 2013)

## Overview
Most requirements ultimately resolve to data: what the organization needs to know, how those facts relate, and which functions create, read, update, or delete them. Owens & Walton argue that a rigorous **data architecture model**, derived directly from how the business describes its world, is the most reliable foundation for requirements - far more stable than feature lists, because the underlying business *entities* change slowly even as functionality churns. This file covers their Integrated Modeling Method (IMM) and step-by-step data architecture modeling, and integrates Wiegers' data elicitation/analysis practices (entity relationship diagrams, data dictionaries, data flow diagrams, CRUD analysis). The unifying principle: model the business's data and processes from its own language, keep design thinking out of the analysis, and link requirements to a clean conceptual data model.

## Core concepts

### Data vs. information; entities, attributes, occurrences
- **Data** are raw facts; **information** is data in a meaningful context. Requirements work needs the logical *structure* of data, not its physical storage.
- An **entity** is a thing of significance to the business about which it needs to hold data (Customer, Order, Product, Address).
- An **attribute** is a property of an entity (a customer's name, an order's date).
- An **occurrence** (instance) is a single specific example of an entity (one particular customer).
- **Synonyms** are different business terms for the same entity; reconciling them is a core analysis task.

### The Integrated Modeling Method (IMM)
Owens & Walton's IMM integrates business **function modeling**, business **process modeling**, and **data architecture modeling** into one coherent method. Requirements gathering feeds all three; the data model and the function/process models cross-check each other (a function that uses data not in the model, or an entity no function touches, signals a gap). The stages of data architecture modeling are:
1. **Requirements gathering** - collect the raw business descriptions.
2. **Modeling for feedback** - build a model to validate understanding with the business.
3. **Feedback** - review and correct with stakeholders.
4. **Modeling for implementation** - refine toward a buildable structure.

### Extracting entities from business language
The signature IMM technique: derive candidate entities by analyzing the **nouns** in business descriptions, using an identification-and-highlighting technique. Sources of entities include existing documents, interviews, and process descriptions. Linguistic cues:
- **Single nouns** → candidate entities.
- **Adjective + noun** → may be a subtype or a distinct entity; sometimes the adjective splits off into its own entity.
- **Group/compound nouns** → may indicate an entity or a relationship.
- **Idioms** → handle with care; not every noun phrase is an entity.
- **Verbs suggesting entities** → some verbs imply a thing of significance.

The process: extract candidates in stages, build a **sorted candidate list**, move attributes and synonyms onto their owning entities, rationalize duplicate associations, then **freeze the list**. Consolidate by deleting duplicate entities, attribute values, and synonyms, then expand. The book warns against being **mechanistic** - judgment is required; not every noun is an entity.

### Data structure diagrams and entity relationships
A **data structure diagram** (Owens & Walton's term; equivalent to an entity relationship diagram, ERD) shows entities and the **relationships** between them. Relationships are read in both directions and characterized by **cardinality**:
- **One-to-many (1:M)** - the common case.
- **Many-to-many (M:M)** - must be **resolved** into an **intersection entity** (a.k.a. associative entity) that captures the association and any attributes of that association. Naming intersection entities and their relationships meaningfully is emphasized.
- **One-to-one (1:1)** - relatively rare; often a sign two entities are really one, or a subtype.
- **Exclusive (arced) relationships** - an occurrence relates to one of several alternatives but not more than one; governed by rules for arcs.
- **Recursive relationships** - an entity related to itself (e.g., employee manages employee).

The **"Dead Crows Rule"** is a layout heuristic for drawing readable data structure diagrams (crow's-foot ends should not point in awkward directions). **Ranking entities** (forming ordered pairs, applying ranking rules) produces a sensible order in which to lay out the diagram.

### Supertypes and subtypes
When occurrences of an entity divide into meaningfully different kinds, model **supertype/subtype** structures. Subtypes can be based on **attributes** (different kinds have different properties) or on **relationships** (different kinds participate in different relationships). Distinguish a true subtype from a mere "Type" lookup entity and from synonyms.

### Unique Identifiers (UIDs)
Every entity needs a **unique identifier** - the business-meaningful way to tell one occurrence from another. UIDs may be:
- One or more **attributes**.
- A combination of **attributes and relationships**.
- One or more **relationships** (common for intersection entities).

Critical rules (Owens & Walton): **codes are NOT UIDs** by default (a code is an attribute that may or may not be unique/stable), and **UIDs cannot be NULL.** Worked examples cover Employee, Customer, and Address.

### Avoiding "design" thinking during analysis
A central IMM discipline: keep **implementation/design concerns out of the logical model.** Primary keys, foreign keys, and physical intersection tables are *design* artifacts; introducing them during analysis corrupts the conceptual model. The logical model should reflect the business's reality, not a database schema.

### Normalization
Owens & Walton tie data architecture to **normalization** (history; 1st, 2nd, 3rd Normal Form; Boyce-Codd Normal Form; up to 5th Normal Form and its relation to data warehousing). A correctly built conceptual data model is typically already well-normalized; normalization theory provides the formal justification and a check that each attribute depends on the whole UID and nothing but the UID.

## Frameworks & models

### Wiegers' data elicitation and analysis
Complementary to IMM, Wiegers & Hokanson (Practice #8) provide elicitation questions and models:
- **Questions:** What data does each component require, and from where? What does each produce that must be retained? Who consumes each piece, and how? What flows in/out and between components, by what mechanism? What constraints/business rules apply? Which system "owns" each object (source of truth)? What must appear on a UI? What governance applies (quality, access, security, privacy, integrity, retention, archiving, disposal)?
- **Conceptual / logical / physical ERDs:** conceptual (business entities and relationships only), logical (adds attributes), physical (tables, columns, keys, referential integrity).
- **Cardinality** with crow's-foot notation: 1:1, 1:M, M:M, one-to-zero-or-more, one-to-one-or-more. Walk the model with stakeholders to verify each relationship ("Must an order always contain at least one item?").
- **CRUD (CRUDCLUMT) analysis:** for each entity, confirm where it is **C**reated, **R**ead, **U**pdated, **D**eleted (and copied, listed, used, moved, transformed). Find data created-but-never-used, or used-but-never-created - each signals a missing process or requirement.
- **Data dictionary:** the detail behind the ERD - every field/attribute with metadata (data type, length, valid values, business rules, required, unique). Distinct from a glossary. Used to align data across systems, drive migrations, and catch type/length mismatches that cause corruption or loss.
- **Data flow diagram (DFD):** a child of the context diagram showing external entities, processes (circles), data stores (parallel lines), and labeled data flows. Cross-check: every data object in a DFD must appear in the ERD.

### Hidden data requirements
Many data requirements come from non-user stakeholders: corporate **data governance** groups, security/integrity policies, and regulations (Sarbanes-Oxley, PII protection laws) that dictate retention, audit logging, encryption, and archiving. Solution/database architects add data-access and performance requirements (e.g., online statements for two years, older statements from offline archive). End users never state these.

## Patterns

- **Noun extraction for entities** - *intent:* derive a complete, business-grounded entity list. *When:* start of data architecture modeling. *Trade-offs:* needs judgment to avoid spurious entities; produces a model anchored in the business's own language.
- **Resolve M:M with an intersection entity** - *intent:* make many-to-many relationships implementable and capture association attributes. *When:* any M:M relationship. *Trade-offs:* adds an entity; essential for a correct, normalized model.
- **Supertype/subtype** - *intent:* model meaningfully different kinds of an entity without duplicating shared structure. *When:* kinds differ by attributes or relationships. *Trade-offs:* over-subtyping adds complexity; distinguish from simple "Type" lookups.
- **CRUD/CRUDCLUMT completeness check** - *intent:* find missing functionality and orphan data. *When:* after the entity list and process flows exist. *Trade-offs:* tabular and tedious; reliably surfaces gaps.
- **Data dictionary alignment** - *intent:* prevent integration data corruption and migration loss. *When:* systems exchange or migrate data. *Trade-offs:* upfront detail; cheaper than fixing length/type mismatches in production.
- **Keep design out of analysis** - *intent:* preserve a business-true conceptual model. *When:* throughout analysis. *Trade-offs:* requires discipline to defer primary keys, FKs, physical tables.

## Principles & best practices
- Model the business's data from its own words; reconcile synonyms before freezing the entity list.
- Resolve every M:M into a named intersection entity; give intersection entities and their relationships meaningful names.
- Every entity has a non-null UID; codes are attributes, not automatically UIDs.
- Cross-check models: function/process models against the data model; DFD data objects against the ERD; CRUD operations against process flows.
- Defer all physical/design decisions (keys, indexes, tables) until after the logical model is stable and validated.
- Treat business rules and cardinalities as inseparable from the data model - relationship multiplicities are often business rules in disguise.
- Validate the data model collaboratively (modeling for feedback), then refine for implementation.
- A clean conceptual model is usually already normalized; use normalization theory to verify, not as the primary design driver.

## Anti-patterns & pitfalls
- **Being mechanistic** about noun extraction - treating every noun as an entity.
- **Leaving M:M relationships unresolved** - not implementable, hides association data.
- **Using codes as UIDs** - codes can be non-unique, reused, or unstable.
- **Allowing NULL UIDs** - breaks identity.
- **Letting design thinking into analysis** - primary/foreign keys and physical tables in the conceptual model corrupt it.
- **Skipping CRUD analysis** - orphan and uncreated data go unnoticed until late.
- **Ignoring data type/length mismatches** between exchanging systems - causes silent data corruption or loss in production.
- **Relying on end users for governance/security/retention requirements** - they will never state them; get them from data governance, security, and regulators.

## Decision guidance
- **Where to start requirements?** Owens & Walton: start from the business's data and processes, not a feature wish-list, because entities are stable while features churn. Build the data architecture model first, then derive functions and processes against it.
- **Entity or attribute or relationship?** A thing the business needs many facts about and tracks instances of → entity. A single property of one entity → attribute. A meaningful association between entities → relationship (resolve M:M into an intersection entity).
- **Subtype or separate entity or "Type" lookup?** Differing attributes/relationships among kinds → supertype/subtype. A simple categorization with no behavioral difference → a "Type" lookup attribute/entity.
- **Conceptual, logical, or physical model - for whom?** Conceptual for business validation; logical (with attributes) for analysts; physical (keys, tables) only at design time, by database architects.
- **Real-time or batch data updates?** Decide per object by business need (bank balance → transactional; credit score → daily batch acceptable); each choice drives different processes and functionality.

## Source attribution
- The Integrated Modeling Method, the staged data architecture modeling process, noun-based entity extraction, candidate-list consolidation, data structure diagrams, entity ranking, the Dead Crows Rule, M:M resolution via intersection entities, exclusive/recursive relationships, supertypes/subtypes, the UID rules ("codes are NOT UIDs," "UIDs cannot be NULL"), avoiding design thinking, and the normalization treatment are all from **Business Requirements Gathering and Data Architecture** (Owens & Walton, 2023).
- Data elicitation questions, conceptual/logical/physical ERDs and crow's-foot cardinality, CRUD/CRUDCLUMT analysis, data dictionaries, data flow diagrams, hidden governance/security/regulatory data requirements, and the model cross-checking discipline are from **Software Requirements Essentials** (Wiegers & Hokanson, 2023, Practice #8) and **Software Requirements, 3rd Edition** (Wiegers & Beatty, 2013).

## Recent developments (2024–2026)
The conceptual data-modeling discipline (entities from business language, ERDs, M:M resolution, supertypes/subtypes, UID rules, CRUD analysis, keeping design out of analysis, normalization) is stable and foundational. The 2024-2026 developments sit mostly above the conceptual model, in how organizations productize, contract, and govern data, plus AI assistance for modeling itself.

- **Data products and a product mindset for data.** A dominant 2025 trend is treating data as governed, discoverable products owned by business domains (data-mesh influence), each with quality metrics, governance, lifecycle, and APIs. This raises the value of Owens and Walton's business-grounded entity modeling: clear conceptual entities are the backbone of a well-defined, owned data product. (Data Crossroads, 2025; McKinsey, 2025)
- **Data contracts as explicit, enforceable schemas.** Data contracts (schemas plus SLAs agreed upfront between producers and consumers) have become a recognized practice to reduce cross-team misalignment, and 2025 research explores AI-driven generation of these contracts. A contract is essentially a formalized, machine-enforceable expression of the data dictionary and cardinality discipline this file describes. (arXiv 2507.21056 "AI-Driven Generation of Data Contracts," 2025; Data Crossroads, 2025)
- **The semantic layer and knowledge graphs for AI-readiness.** Organizations are investing in semantic layers, ontologies, taxonomies, and knowledge graphs to contextualize data for search, automation, and reasoning, making AI systems able to use enterprise data reliably. A clean conceptual model in the business's own language is the natural input to this semantic layer, reinforcing the IMM's "model from the business's words" stance. (Enterprise Knowledge, 2025; GroupBWT, 2025)
- **AI assists data modeling, but judgment still governs.** AI tools can now propose entities and relationships, draft data dictionaries, and suggest normalization fixes, accelerating the noun-extraction and candidate-list work, while the book's "do not be mechanistic" warning becomes the human guardrail: not every AI-suggested entity is real, and design thinking must still be kept out of the conceptual model. (UseDataBrain, 2025; Data Crossroads, 2025)
- **Governance and AI governance converge.** 2025 guidance pushes unifying data governance and AI governance under one umbrella, with enforceable policies, lineage, and elimination of duplicate datasets, expanding the "hidden data requirements" (retention, audit, PII, encryption) this file flags to include AI-specific concerns such as training-data provenance, consent, and model-input quality, driven partly by the EU AI Act. (KPMG "Data governance in the age of AI," 2025; European Commission, 2024-2025)
