# Project Troubleshooting and Realities

**Domain:** 01 - Foundations and SDLC Process
**Primary sources:** Hardware and Software Projects Troubleshooting: How Effective Requirements Writing Can Save the Day, 2nd ed. (Koelsch, 2024, Apress); with supporting context from Software Engineering, Global Edition, 10th ed. (Sommerville, 2021), chapters 22-23 (project management/planning) and 10 (dependability).

## Overview
Most troubled hardware and software projects fail for the same underlying reason: **bad requirements**. Koelsch's central thesis is that effective requirements writing is what saves a project, because requirements are the foundation of everything that follows - design, code, test, and acceptance. Errors at the requirements stage propagate and compound: fixing a requirement defect after deployment can cost 100 to 200 times more than fixing it during requirements definition. The requirements engineer (RE) is a **translator** between what users say they need and what the IT/development team needs, and between users and coders (who think in computer-logic terms, not user terms). Communication is, across Koelsch's career, the single most common root cause of project problems - more than technology - and a common thread is people who assume "technology fixes all problems" and stop listening.

The "troubleshooting" method in this book is diagnostic and preventive rather than reactive crisis-management: identify the failure modes that derail projects, apply a toolkit of requirements techniques and governance to prevent or mitigate each one, and write requirements that are verifiable, unambiguous, complete, and traceable. The same approach applies to hardware, software, or combined projects.

## Core concepts

**The RE as translator and facilitator.** The RE converts stakeholder statements into precise, testable requirements - essentially writing a "contract" between users and IT. A key move is stripping out *how* (implementation) the stakeholder proposes and capturing *what* they actually need, often generalizing (e.g., "I need a red flashing button when this error happens" → "when an error condition occurs, the system shall display a message of what is wrong and how to proceed"). The most-asked question in the RE's career is **"Why?"** - used to uncover the real need behind a stated request (a user asking to export query results to a spreadsheet may really need to reorder and resize columns the current tool cannot).

**Personal traits that matter for diagnosis and recovery:** flexibility/adaptability, even temperedness and calmness, confidence (without arrogance), and good communication skills - the ability to respond to needs, translate ideas, moderate disagreements, and be persuasive. Listening is at least as important as speaking and writing.

**Requirement form and quality.** Requirements are typically written as "shall" statements. A good requirement must be **atomic** (decomposed to its lowest logical level - split on connecting words like *and, or, nor, yet, but*, except where a conjunction expresses a genuine condition), and organized into **parent-child** hierarchies with a numbering scheme that shows the relationship (e.g., 3.1.1 parent, 3.1.1.1-3.1.1.5 children). Atomicity matters operationally: one compound requirement covering ten functions fails 100% when one part fails, whereas split requirements fail only 50%, telling you precisely what broke and letting you allocate work uniquely to developers.

**Attributes of a good requirement** (Koelsch's comprehensive list, mnemonic "CUD CRUD and CAV, IT CAPUT"): Accurate, Atomic, Complete, Concise, Consistent (does not conflict with others), non-Duplicating, Correct, Realistic, Unambiguous (single interpretation), Verifiable, Traced to a source, and others. Two are worth special emphasis:
- **Verifiable** - if you cannot verify a need has been met, the need is useless; you must be able to envision a verification method (testing, demonstration, inspection, analysis). Unverifiable requirements ("so easy my great-great-grandfather could use it"; "shall operate on the surface of Jupiter") cannot be validated.
- **Complete** - at the individual level, all information needed to define the function, action, and result; at the set level, everything inside and on the boundary of the system. Use **TBD** (To Be Determined) and **TBR** (To Be Reviewed) placeholders with a **work-off plan** (a tracked list of each TBD/TBR and how/when it will be resolved) so incomplete items are not lost.

**Completeness, boundaries, and gap analysis.** Picture the project as a circle inside the rectangle of "everything else"; you are complete when everything inside the circle and on its boundary is described - every action, performance need, design/architectural/policy/environmental constraint, and external interface with all inputs and outputs. **Gap analysis** finds the missing capabilities by asking what the system should do that has not been discussed ("thinking around corners" for unusual conditions - e.g., a phone surviving a three-foot drop onto concrete, a battlefield device surviving rough handling, while EMP survivability is *not* reasonable if the supporting infrastructure would not survive). For each data element, ask why it should be in the system and how it will be used (do not collect a Social Security number just to deliver a newspaper; do not carry forward a legacy field whose original reason no longer applies).

## Frameworks & models

### Catalog of project failure modes (root causes)
Koelsch's diagnostic catalog of the most common and important challenges that produce troubled projects:
1. **Insufficient requirements** - some or all requirements missing; the extreme is *no requirements* ("just let me code" → "code what?"). Leaves gaps in the system description; functions or auditing get omitted. (Distinct from agile just-in-time requirements, where all needed requirements must be present *when needed*.)
2. **Scope: ill-defined boundaries** - vague or poorly defined project boundaries are one of the biggest sources of requirements problems; any "maybe" about whether something belongs (e.g., does the personnel system own salary, next of kin?) signals a boundary that must be negotiated, often across organizations up to a common manager.
3. **Requirements creep (scope creep)** - requirements grow significantly from initial definition to completion (the classic cost overrun); driven by the unreasonable assumption that nothing changes.
4. **Volatility** - already-existing requirements change (not added), e.g., availability raised from 99% to 99.999% when the system's mission changes; far more expensive than the original estimate.
5. **Stove-piped (siloed) requirements** - the project works in a vacuum, especially in large or security-constrained organizations, causing duplicated effort (every project building its own access control, report generator, etc.).
6. **Requirements do not reflect what users/stakeholders need** - users are unsure what they need or are unfamiliar with how systems are developed; the RE must guide them with general-to-detailed questions.
7. **User needs not satisfied** - at delivery, users reject the system or revert to the old one because requirements were missed or written without focus on the real need (capturing "how many records were added" when users needed "who changed which records").
8. **Multiple interpretations cause disagreements** - ambiguous language; the same word means different things to different backgrounds (e.g., "recall" = remove a bad record vs. retrieve archived documents) or is imprecise ("time" without time zone / standard vs. daylight).
9. **Wasted time and resources building the wrong functions** - too much emphasis on low-importance items (89% of effort on 20% of users) or developers/managers adding unrequested "neat" functions; fixed by assigning priorities and approving all changes regardless of methodology.
10. **Adversely impacts the schedule** - REs given too little time (requirements should be ~4-10% of total development effort depending on size/complexity/breadth); arbitrary schedules set without RE input; too much process vetting can prevent finishing requirements before a sprint.
11. **Adversely impacts communication** with users/stakeholders or the dev/test team - candidate requirements not shared for correctness, practicality, or verifiability checks.
12. **Priorities not adequately addressed** - wrong priorities steer resources away from critical functions; developers code in their own order; management overrides stakeholder priorities.

(Failure modes 10-12 are largely outside the RE's direct control and are organizational/process problems.)

### The requirements toolkit (15 techniques)
Koelsch organizes prevention/recovery around 15 tools, each a chapter, that are applied against the failure modes: (1) attributes of a good requirement, (2) types of errors (dangerous/toxic, superfluous, incomplete requirements), (3) specialized language/glossary, (4) functional requirements, (5) nonfunctional requirements, (6) lists of items and order of steps/elements, (7) data interfaces and documents, (8) physical/hardware requirements, (9) requirements elicitation/collection, (10) user-interface requirements, (11) requirements management (tools), (12) supplementing/replacing standard requirements (modeling, graphical representation), (13) user stories, (14) use cases, and (15) requirements governance.

### Problem-to-tool impact matrix (the diagnostic method)
The book's distinctive recovery method (chapter 16) is a matrix that rates each tool's impact on each failure mode on a scale of **None / Very low / Low / Medium / High / Maximum**, then states the combined effect. Examples:
- **Requirements creep** and **volatility** - best cured by **just-in-time requirements** (define requirements as close as possible to the implementation/sprint where the work is done; do not define everything up front and freeze it) plus **requirements governance** (High impact: many more reviewers catch creep/volatility). Combined effect: very high to maximum.
- **Stove-piped requirements** - best prevented organizationally (have one development shop, or reach out and reuse others' requirements); governance has Medium impact; a **champion** who shares your common requirements is essential for the culture change. Combined effect: medium to high.
- **Ill-defined scope** - at project start, get the major players to agree on the scope and exactly where it ends and other systems begin; elicitation and governance have Medium impact. 
This matrix is how an RE diagnoses a troubled project: identify which failure mode(s) are present, then apply the tools with the highest rated impact on those modes.

### Requirements governance
Bringing many more people to review the requirements (beyond the RE and immediate team) to catch creep, volatility, stove-piping, and scope problems. Acts as a broad, repeated review/inspection layer over the requirements set.

### User stories and use cases as complements to "shall" statements
"Shall" statements serve developers and testers; **user stories** ("As a `<role>`, I want/need to `<do something>`") are written in terms the user understands and are better for eliciting and validating needs with stakeholders; **use cases** capture interaction flows. They complement each other - user stories are weaker at capturing nonfunctional needs, so pair them with shall statements and trace between them. A real-world recovery: when traditional requirements gathering stalled (six-eight months for one of a dozen functions, "five years at this rate"), the team switched to capturing user stories with stakeholders, then did gap analysis and wrote shall statements for the dev/test teams, with tracing between them - completing all functional areas in the same time the old approach took for one.

## Patterns
- **RE-as-translator / ask "Why?"** - *Intent:* capture the real need behind a stated request and strip out premature implementation detail. *When:* every elicitation interaction. *Trade-offs:* requires strong listening and rapport; takes practice.
- **Atomic, parent-child requirements** - *Intent:* decompose to the lowest logical level so failures localize, work allocates cleanly, and testing is precise. *When:* writing any requirement set. *Trade-offs:* more individual statements to manage; do not split genuine conditional clauses.
- **Verifiability gate** - *Intent:* accept no requirement you cannot envision verifying. *When:* writing and reviewing requirements; have the test team validate them. *Trade-offs:* forces precision that some stakeholders find tedious.
- **Completeness with TBD/TBR and a work-off plan** - *Intent:* never lose an open item; track each placeholder to resolution. *When:* values or limits are not yet known. *Trade-offs:* the requirement is incomplete until resolved; needs disciplined tracking.
- **Gap analysis / "thinking around corners"** - *Intent:* find missing capabilities and unusual operating conditions before users do. *When:* before finishing requirements definition. *Trade-offs:* requires creativity; risk of over-engineering for implausible conditions.
- **Just-in-time requirements** - *Intent:* define requirements as close to implementation as possible to defeat creep and volatility. *When:* agile/iterative work; long projects. *Trade-offs:* needs disciplined elicitation cadence; heavy process vetting can break it.
- **Requirements governance** - *Intent:* broad, repeated review to catch creep, volatility, stove-piping, and scope problems. *When:* any non-trivial project. *Trade-offs:* coordination overhead; more reviewers to manage.
- **Champion-led culture change** - *Intent:* secure an advocate to break down silos and spread reusable common requirements. *When:* fixing stove-piped requirements across organizational boundaries. *Trade-offs:* hard early in a career; depends on finding the right champion.
- **Scope boundary agreement up front** - *Intent:* get major players to agree exactly where the project ends and other systems begin. *When:* project start, especially with cross-organization interfaces. *Trade-offs:* negotiation can be slow and political (budget, egos).

## Principles & best practices
- Treat requirements as the foundation; getting them right early is the cheapest place to prevent trouble (defects cost 100-200x more after deployment).
- Be a translator: capture *what* users need, not *how* they propose to implement it, and generalize where sensible.
- Ask "Why?" relentlessly to uncover real needs.
- Write atomic, parent-child, uniquely identified, verifiable, unambiguous, complete, and traceable requirements; establish a common glossary to kill ambiguity.
- Define the scope and boundaries precisely at the start, including where other systems begin.
- Assign and respect priorities so effort tracks importance, and require approval for all changes (developer- or manager-initiated) regardless of methodology.
- Allocate adequate time for requirements (~4-10% of development effort) and get RE input into the schedule.
- Share candidate requirements with stakeholders (correctness/priority), developers (practicality), and testers (verifiability).
- Use just-in-time requirements and governance to defeat creep and volatility; reuse common requirements to defeat stove-piping.
- Use user stories and use cases to complement shall statements, and trace between them.
- Listen more than you speak; communication failure is the most common root cause of troubled projects.

## Anti-patterns & pitfalls
- **No or insufficient requirements** - coding without knowing what to build; gaps discovered only at delivery.
- **Capturing the implementation instead of the need** - locking in *how* rather than *what*, missing the general requirement.
- **Compound, non-atomic requirements** - failures and work allocation cannot be localized.
- **Unverifiable requirements** - needs no one can confirm were met.
- **Defining everything up front and freezing it** - invites scope creep and volatility.
- **Ambiguous or imprecise language** - multiple interpretations cause design disagreements and wrong systems.
- **Building the wrong functions** - effort skewed to low-importance users, or developers/managers adding unrequested features at the expense of needed ones.
- **Stove-piped/siloed work** - reinventing common functions (access control, reporting) project by project.
- **Arbitrary schedules without RE input** and **too much process vetting** - REs cannot finish requirements before they are needed.
- **Assuming technology fixes everything and not listening** - the recurring root of communication failure.
- **Premature freezing of requirements** (shared with the waterfall failure mode) - problems get worked around rather than fixed.

## Decision guidance
- To triage a troubled project, first diagnose which failure mode(s) are present (use the catalog above), then apply the tools with the highest rated impact on those modes (the problem-to-tool matrix is the recovery roadmap).
- For creep/volatility: shift to just-in-time requirements and add requirements governance (highest combined impact).
- For stove-piping: fix it organizationally (consolidate development or reuse others' requirements) and recruit a champion to drive the culture change; target common functions (reporting, printing, access control, search) first.
- For ill-defined scope: convene the major players at project start to agree the boundary, including where other systems begin; lean on elicitation and governance.
- For "wrong functions"/priority problems: assign and enforce priorities so effort tracks importance, and require change approval regardless of methodology.
- For ambiguity: build a shared glossary and write single-interpretation, precise requirements; have testers validate verifiability.
- Match requirements style to the development model: shall statements for dev/test, user stories for stakeholder elicitation and validation, use cases for interaction flows, with tracing between them; in agile, define requirements just before the sprint that needs them while ensuring all needed requirements are present when needed.

## Source attribution
- The thesis that effective requirements prevent troubled projects; the RE-as-translator role; the catalog of failure modes (insufficient requirements, scope/boundaries, creep, volatility, stove-piping, needs-not-reflected, needs-not-satisfied, ambiguity, wrong functions, schedule/communication/priority impacts); attributes of a good requirement; atomicity and parent-child requirements; verifiability; completeness, TBD/TBR, work-off plans, gap analysis, and "thinking around corners"; cost-of-fixing escalation (100-200x); the requirements time budget (~4-10%): Koelsch, *Hardware and Software Projects Troubleshooting*, 2nd ed., 2024, chapters 1-2.
- The 15-tool toolkit and the problem-to-tool impact matrix (None→Maximum), just-in-time requirements, requirements governance, champion-led culture change, scope-boundary agreement, and user stories/use cases as complements to shall statements: Koelsch, chapters 9, 11, 13-16.
- Supporting project-management/planning and risk context (project management, planning, configuration management) and dependability cost considerations: Sommerville, *Software Engineering, Global Edition*, 10th ed., 2021, chapters 22-23 and 10.

## Recent developments (2024–2026)
Koelsch's core thesis (bad requirements are the root cause of troubled projects, and communication is the most common failure) is reinforced, not overturned, by recent trends. AI changes the toolkit for diagnosis and prevention but also introduces new failure modes.

- **AI as a requirements-quality assistant against several failure modes.** Requirements tools now use AI to score statements against quality standards and flag ambiguity, passive voice, and missing tolerances during authoring (for example, Jama Connect Advisor against INCOSE/EARS, and IBM DOORS Next's Watson-based Requirements Quality Assistant). This directly attacks Koelsch's hardest attributes (unambiguous, verifiable, complete) and his "multiple interpretations" failure mode at the point of writing. (Jama Software, 2025-2026, jamasoftware.com/blog/ai-requirements-management; Jama Software on DOORS, 2025)
- **Faster drafting and trace maintenance, with human review still required.** Generative AI is reported to cut requirements drafting and trace-matrix maintenance time substantially (some regulated teams citing up to 70% reductions), helping with the schedule-impact failure mode, but the output still requires human validation, consistent with Koelsch's insistence that the RE remains the translator who confirms the real need. (Jama Software, 2025)
- **A new failure mode: over-trusting AI output ("vibe coding").** 2025 studies report AI-generated code carries materially more defects and vulnerabilities (Veracode: ~2.74x) and that developers tend to accept it with insufficient scrutiny. This is a modern instance of Koelsch's "assume technology fixes everything and stop listening" root cause, now applied to generated code rather than just generated requirements. (Veracode, 2025; LeadDev, 2025)
- **Spec-driven development operationalizes "requirements first."** The 2025 SDD movement (GitHub Spec Kit, Amazon Kiro) makes an explicit requirements artifact the source of truth an AI agent builds against, with Kiro adopting EARS-style requirements. This is essentially Koelsch's argument (get requirements right before coding) re-expressed for the AI era, and a counter to the "just let me code" anti-pattern. (GitHub Blog, 2025; Kiro, 2025)
- **Requirements creep and volatility now arrive faster.** Because AI shortens build time, stakeholders request changes more readily, raising the importance of Koelsch's just-in-time requirements plus governance combination; the cost of a poorly assessed change still ripples, and impact analysis (now AI-assisted in tools like Visure and Jama) remains the safeguard. (Jama Software, 2025; Visure, 2025)
