# Requirements for Systems Engineering

**Domain:** 02 - Requirements & Specifications
**Primary sources:** Requirements Writing for System Engineering (Koelsch, 2016); Software Requirements Essentials (Wiegers & Hokanson, 2023); Software Requirements, 3rd Edition (Wiegers & Beatty, 2013)

## Overview
Systems engineering raises the stakes on requirements writing. Products that combine hardware and software, are safety- or mission-critical, are outsourced, or must be formally certified cannot tolerate the loose, conversational requirements that a co-located agile team might get away with. This file covers how to write a single, well-formed requirement statement - the "shall" convention, the grammatical form, decomposition to atomic level, and handling of negatives and lists - plus the verification methods and traceability practices that make systems-engineering requirements auditable. The requirements engineer (RE) writes statements precise enough that a third party can build and *verify* each one. The economic argument is stark: fixing a requirement after deployment can cost 100–200x more than fixing it during the requirements phase (Davis, cited by Koelsch), so it is imperative to get requirements right and to review (validate) them early.

## Core concepts

### The form of a requirement
A requirement statement has a consistent grammatical form built around an actor, the keyword **shall**, and a capability or behavior. Koelsch's conventions:
- Each statement carries a unique identifier (e.g., `1-1`, or a meaningful hierarchical tag) so it can be referenced unambiguously.
- `DRAFT-` prefixes a statement that may or may not yet be a good requirement.
- `PARENT-`/`CHILD-` mark decomposition relationships.

Example forms:
- `The BOSS Access Control Function shall allow an authorized user to access the system.`
- `When <conditions>, the <system> shall <do something>.`

### Why "shall"
A single, consistent modal verb avoids the ambiguity of mixing **shall, must, will, may, should, can, could** - a mix that makes readers wonder whether different verbs imply different levels of necessity or priority. Some government standards *do* assign precise meanings to each; absent such a standard, sticking to **shall** for functional requirements is the least confusing choice. (Some organizations reserve `will` for statements of fact about the environment and `should` for goals.)

### Atomic decomposition and parent–child requirements
Each requirement must be defined at the **atomic level** - decomposed to its lowest logical level, expressing exactly one testable thing. When a high-level requirement naturally subdivides, capture it as a **parent** with subordinate **child** requirements:
- `PARENT - The BOSS Audit Function shall allow system administrators to generate an audit report.`
- `CHILD - The BOSS Audit Report shall include all login attempts, all failed login attempts, and who attempted the login.`

Atomicity supports the **traceable**, **verifiable**, and **does-not-conflict/does-not-duplicate** attributes: you cannot cleanly trace or verify a compound requirement.

### The 16 attributes of a good requirement
Koelsch's checklist (asterisks mark those most emphasized): accurate/correct\*, atomic, complete\*, concise, consistent\*, does-not-conflict, does-not-duplicate, independent, prioritized\*, realistic, traceable\*, traced-to-a-source, **unambiguous\*** (the hardest - "a knowledgeable person interprets each statement only one way"), understandable-by-stakeholders, unique, **verifiable\***. The set is not gospel; other texts show more or fewer.

### Types of requirements an RE must cover
- **Functional types:** business rules; transaction corrections/adjustments/cancellations; administrative functions; authentication; authorization levels; audit tracking; external interfaces; certification requirements; searching/reporting; historical data; archiving; compliance/legal/regulatory; structural; algorithms; database; power; network; infrastructure; backup and recovery.
- **Nonfunctional types:** architectural, capacity, constraints, documentation, efficiency, and the broader quality-attribute set (availability, reliability, security, maintainability, etc. - see `requirements-types-and-quality.md`).

Many "types" apply to both hardware and software in a systems context.

## Frameworks & models

### The five verification methods
A core systems-engineering discipline: for each requirement, the RE proposes a candidate **verification method** (you choose the method, not the detailed procedure). The standard set:
- **Test** - exercise the system with defined inputs and instrumentation; compare measured outputs to expected.
- **Demonstration** - show that the product complies by observing results, usually without measurement or instrumentation.
- **Analysis** - quantitative evaluation by reviewing collected data or modeling (e.g., compute mean-time-before-failure from collected run times: `The BOSS game device shall have a mean time before failure of 200 hours`).
- **Inspection** - examine the item (documents, code, physical characteristics) for conformance.
- **Certification / special verification** - compliance demonstrated against an external standard or certifying authority (e.g., `The BOSS shall meet the Security Certification specified by <organization>` - which itself may spawn derived requirements).

If you cannot identify a reasonable verification method for a statement, it is **not verifiable** and therefore not a good requirement. The verification method is recorded as a requirement attribute and is the seed of the later verification/test program.

### Verification vs. validation
- **Verification** - "did we build it right?" Confirm the implementation meets the requirement (test, demo, analysis, inspection, certification).
- **Validation** - "did we build the right thing?" Confirm the requirements actually describe what stakeholders need; performed by users/stakeholders and the test team reviewing requirements. The team can verify on its own but cannot validate requirements without stakeholder input.

### Traceability (and the traced vs. traceable distinction)
Koelsch (following IEEE 830-1998 and Davis) distinguishes:
- **Traced (to a source)** - each requirement points backward to its origin (a stakeholder, a higher-level requirement, a business rule, a regulation). Every requirement should be traced to a source; a low-level requirement with no parent may be extraneous, and a high-level item with no children may signal missing requirements.
- **Traceability (forward)** - each requirement links forward to the design specifications, code, and tests that address it.

Bidirectional traceability supports impact analysis, regression testing, certification audits, and change management. It requires fine-grained, uniquely identified requirements and tool support. Full traceability is mandatory for safety-critical and regulated products; for ordinary business systems it is desirable but often partial. (See `requirements-management-and-change.md`.)

### Requirement boilerplates / patterns
A boilerplate is a fixed sentence template with slots, e.g.:
- Capability: `The <system> shall be able to <capability> [within <performance constraint>].`
- Conditional: `When <trigger/condition>, the <system> shall <response> [within <time>].`
- Constraint: `The <system> shall <comply with / not exceed> <standard or limit>.`

Boilerplates (and the broader pattern catalogs in Withall's *Software Requirement Patterns*) make requirements consistent, complete, and machine-checkable, and reduce ambiguity by forcing the author to fill known slots.

## Patterns

- **Verification method per requirement** - *intent:* guarantee verifiability and seed the test program. *When:* every requirement in a systems-engineering context. *Trade-offs:* extra column/attribute to maintain; forces clarity and catches unverifiable statements early.
- **Parent–child decomposition** - *intent:* manage complexity and reach atomic, testable statements. *When:* high-level requirements with internal structure. *Trade-offs:* more statements to manage; enables clean tracing and verification.
- **Boilerplate / requirement pattern** - *intent:* consistency, completeness, reduced ambiguity. *When:* large or formal requirement sets, especially with non-native-language readers. *Trade-offs:* can feel rigid; far easier to review and tool-check.
- **TBD with work-off plan** - *intent:* explicitly flag incomplete requirements awaiting a decision. *When:* a value or detail is genuinely deferred to an expert. *Trade-offs:* a TBD means the requirement is incomplete; track every one to closure.
- **Bidirectional traceability matrix** - *intent:* link requirements to sources and to design/code/tests. *When:* certification, regulated, safety-critical, or large multi-team systems. *Trade-offs:* significant effort and tooling; indispensable for impact analysis and audits.

## Principles & best practices
- One requirement per statement, atomic and uniquely identified; no compound "and/or/except" requirements.
- Use **shall** consistently for mandatory functional behavior; do not casually mix near-synonyms.
- Quantify wherever possible (use numbers, ranges, units, tolerances) so the requirement is measurable and verifiable.
- Prefer positive phrasing; the universe of "shall not" behaviors is infinite, so write what the system *shall* do.
- For every requirement, ask "how would I verify this?" - if no method fits, rewrite it.
- Trace every requirement to a source and forward to design/test; investigate orphans (no parent) and childless high-level items.
- Resolve every TBD before baselining; maintain a TBD work-off list.
- Let stakeholders with institutional knowledge review requirements to catch ambiguity that distributed developers would miss.
- Use a glossary and prefer a single specific phrase over multiple loose synonyms to reduce ambiguity and glossary bloat.

## Anti-patterns & pitfalls
- **Verb soup** - randomly mixing shall/will/should/may, implying distinctions that are not defined.
- **Compound requirements** - multiple behaviors in one statement, defeating atomicity, tracing, and verification.
- **Unverifiable statements** - no conceivable test/demo/analysis/inspection/certification; the most common systems-engineering defect after ambiguity.
- **Ambiguity** - the hardest attribute; a statement that "makes sense" to each reader but means something different to each. It never fully goes away.
- **Leaving TBDs unresolved** - shipping requirements that are by definition incomplete.
- **Embedding design** - specifying *how* when only *what* is required; confirm whether implementation-specific language is a genuine constraint or just a suggestion (see `requirements-specification-and-design-bridge.md`).
- **No source trace** - requirements that cannot be justified when challenged, and orphans that may be extraneous.
- **Skipping early validation** - relying on post-deployment feedback, the most expensive way to discover a wrong requirement.

## Decision guidance
- **How formal should requirements be?** Drive by risk and contract. Hardware+software, safety-/mission-critical, outsourced, or certified systems demand strict "shall" statements, full traceability, and a documented verification method per requirement. Lower-risk software can be lighter (but still atomic, unambiguous, and verifiable).
- **Which verification method?** Choose the cheapest method that gives adequate confidence: Inspection/Demonstration for presence and observable behavior; Test for precise functional/performance behavior with instrumentation; Analysis for properties provable from collected data or models (reliability, capacity); Certification when an external authority must attest compliance.
- **System vs. software requirements?** Start a complex product with a System Requirements Specification (SyRS) of top-level behaviors, then derive software and hardware sub-specifications, allocating each requirement to a subsystem and tracing the allocation.
- **When to decompose?** Decompose a requirement to atomic level whenever it bundles multiple behaviors, conceals significant complexity, or cannot be verified as a single statement.

## Source attribution
- The "shall" conventions, requirement form, DRAFT/PARENT/CHILD notation, atomic decomposition, the 16 attributes, the functional/nonfunctional type catalogs, the five verification methods, the traced-vs-traceable distinction, negatives handling, and TBD discipline are all from **Requirements Writing for System Engineering** (Koelsch, 2016, especially Ch. 2, 4, 5, 11).
- Verification-vs-validation framing, the SyRS-to-sub-spec hierarchy, and requirement patterns/boilerplates (Withall) are reinforced by **Software Requirements Essentials** (Wiegers & Hokanson, 2023, Practices #14, #15, #18) and **Software Requirements, 3rd Edition** (Wiegers & Beatty, 2013).

## Recent developments (2024–2026)
Systems-engineering requirements practice (the "shall" form, atomic decomposition, the five verification methods, verification vs. validation, traceability) is stable and standards-driven. Recent developments refresh the standards baseline and add AI assistance and patterned syntax.

- **Updated standards baseline.** The INCOSE Guide to Writing Requirements V4 (July 2023) aligns with INCOSE Systems Engineering Handbook V5, the Needs and Requirements Manual, and the Guide to Verification and Validation, and codifies 42 rules and 15 quality characteristics (necessary, appropriate, unambiguous, complete, singular, feasible, verifiable, and more) that closely mirror Koelsch's 16 attributes. These are now the reference standard for systems-engineering requirements. (INCOSE, 2023, incose.org)
- **EARS notation complements "shall."** EARS (Easy Approach to Requirements Syntax), developed at Rolls-Royce, provides constrained patterns (ubiquitous, event-driven "When...", state-driven "While...", optional-feature "Where...", unwanted-behavior "If...then...", and complex combinations) that reduce ambiguity and make requirements more consistent and tool-checkable. EARS plus INCOSE rules is a common 2024-2025 pairing for high-quality, testable requirements. (Jama Software, 2024-2025; Visure, 2025)
- **AI quality assistants for systems requirements.** Tools now score requirements against INCOSE/EARS, flag ambiguity, passive voice, and missing tolerances, and suggest linked verification/test cases at authoring time (Jama Connect Advisor; IBM DOORS Next Requirements Quality Assistant). This automates the verifiability and unambiguous gates and helps seed the verification method per requirement. (Jama Software, 2025-2026; IBM, 2025)
- **AI-assisted traceability for certification-grade work.** Generative AI is being applied to build and maintain bidirectional trace matrices and impact analysis, with reported large time savings in regulated settings, lowering the cost barrier this file flags for full traceability while human sign-off remains required. (Jama Software, 2025; Visure, 2025)
- **Regulation broadens what must be specified and verified.** The EU AI Act (in force August 2024, high-risk obligations by August 2026) adds requirements on risk management, data quality, transparency, human oversight, and accuracy for high-risk AI systems, and the revised EU Product Liability Directive (in force December 2024) treats software defects and unpredictable AI behavior as liability triggers, raising the stakes on verifiable, traceable requirements and validation for AI-intensive systems. (European Commission, 2024-2025; Pinsent Masons, 2024)
