# Software Engineering Fundamentals

**Domain:** 01 - Foundations and SDLC Process
**Primary sources:** Software Engineering, Global Edition, 10th ed. (Sommerville, 2021), chapters 1, 10, 24; Software Engineering: Specification, Implementation, Verification (Alagić, 2017).

## Overview
Software engineering is an engineering discipline concerned with all aspects of software production, from early system specification through to maintaining the system after it has gone into use. Two phrases in that definition carry the weight. "Engineering discipline" means engineers make things work: they apply theories, methods, and tools selectively, work within organizational and financial constraints, and make compromises rather than seek perfection. "All aspects of software production" means the discipline is not just the technical act of programming but also project management and the development of the tools, methods, and theories that support development.

Software, in the engineering sense, is not just programs but also all associated documentation, libraries, support websites, and configuration data needed to make the programs useful. Professional software is intended for use by people other than its developer, is usually built by teams, and is maintained and changed throughout its life. This is what distinguishes professional software development from personal programming and is why software engineering exists: individuals and society depend on advanced software systems, and it is cheaper in the long run to engineer professional software than to write programs ad hoc (failure to use engineering methods raises testing, quality-assurance, and long-term maintenance costs).

## Core concepts

**Two kinds of software product.** *Generic (off-the-shelf) products* are stand-alone systems sold on the open market; the developer controls the specification. *Customized (bespoke) products* are commissioned by and built for a particular customer, who controls the specification. The distinction is blurring as systems are increasingly built on a generic base (e.g., ERP systems from SAP/Oracle) then adapted.

**Essential attributes of good software** (Sommerville's four):
- **Acceptability** - understandable, usable, and compatible with the systems users already use.
- **Dependability and security** - should not cause physical or economic damage on failure; must resist malicious access or damage.
- **Efficiency** - should not waste resources (responsiveness, processing time, memory, etc.).
- **Maintainability** - should be written so it can evolve to meet changing needs, since change is inevitable.
No system can be optimized for all quality attributes simultaneously (e.g., improving security may cost performance), so the quality plan must declare which attributes matter most.

**Software engineering fundamentals that apply to all systems:** a managed and understood development process; dependability and performance; understanding and managing the specification/requirements; and effective reuse of existing software. These themes (process, dependability, requirements, management, reuse) are independent of programming language.

**Software engineering diversity.** There are no universal methods; the most significant factor in method selection is the application type. Categories include stand-alone applications, interactive transaction-based applications, embedded control systems, batch processing systems, entertainment systems, modeling/simulation systems, data collection and analysis systems, and systems of systems. Each needs specialized techniques (a safety-critical car braking controller needs extensive V&V; an interactive web app needs iterative development with reusable components).

**Cross-cutting challenges:** heterogeneity (distributed systems across diverse devices and legacy systems), business and social change (need for faster development and evolution), security and trust, and scale (from tiny embedded systems to internet-scale cloud systems).

**Relationship to neighboring disciplines.** Computer science is concerned with theory and fundamentals; software engineering with the practicalities of producing useful software. Systems engineering is concerned with all aspects of computer-based systems including hardware, policy, and process; software engineering is part of it.

## The four process activities (fundamentals view)
Every process, however organized, contains:
1. **Specification** - define functionality and operating constraints.
2. **Development** - design and program the software.
3. **Validation** - check the software does what the customer needs.
4. **Evolution** - modify to reflect changing needs.
(See sdlc-and-process-models.md for how these are sequenced or interleaved across process models.)

## Dependability and security

Coined by Jean-Claude Laprie (1995, revised 2004) to cover related system attributes under one term because they are inextricably linked. Dependability reflects a system's **trustworthiness** - the degree of confidence that it will operate as expected and not fail in normal use. It is not meaningful to express numerically; use relative terms (not dependable, very dependable, ultra-dependable).

**The five dependability properties:**
1. **Availability** - probability the system is up and able to deliver service at any given time.
2. **Reliability** - probability that, over a period, the system correctly delivers services as the user expects. (Decomposes into correctness, precision, timeliness.)
3. **Safety** - how likely the system is to cause damage to people or environment.
4. **Security** - how likely the system can resist accidental or deliberate intrusion. (Decomposes into integrity and confidentiality.)
5. **Resilience** - how well the system maintains continuity of critical services under disruptive events (failures, cyberattacks); a more recent addition to Laprie's original set.

Related influencing properties: **repairability** (quick diagnosis and fix), **maintainability** (economical adaptation with low risk of introducing new errors), and **error tolerance** (designing so user-input errors are avoided, detected, and tolerated).

Not all properties are critical for every system: an insulin pump prioritizes reliability and safety (and has no security concern if not networked); a patient records system prioritizes security and resilience; a remote weather station prioritizes availability and reliability.

**Achieving dependable software** requires you to: avoid introducing accidental errors during specification and development; design effective V&V to find residual errors; design for fault tolerance so the system keeps working when things go wrong; build protection mechanisms against external attack; configure the deployed system correctly; recognize and resist cyberattacks; and design for fast recovery without loss of critical data. The fundamental mechanisms are **redundancy and diversity** (extra components and different ways of doing the same thing so a single fault does not cause failure). Fault tolerance adds redundant checking code, so designers trade off performance against dependability.

**Sociotechnical systems perspective.** Software is never isolated; it executes in an environment of hardware, human users, and organizational processes. Failures arise from hardware failure, software failure, and increasingly **operational failure** (humans not operating the system as intended); these interrelate (a hardware failure stresses operators, who make mistakes, which stress the software). Dependable-systems engineering must therefore take a holistic view rather than optimizing software or hardware in isolation, because errors cluster at the interfaces between separately designed parts.

**Cost of dependability.** Increasing dependability raises design, implementation, and validation costs, and the cost rises steeply at the high end (the cost/dependability curve flattens in benefit as it steepens in cost). Demonstrating dependability by testing also gets harder as the system gets more dependable, since failures become rarer and more tests are needed to estimate remaining defects. Ultra-dependable safety-critical systems may also have to prove safety to an external regulator.

## Quality management

Software quality management ensures developed systems are "fit for purpose": meeting user needs, performing reliably and efficiently, delivered on time and on budget. It operates at two levels: **organizational** (a framework of processes and standards for high-quality software) and **project** (applying specific quality processes, checking they were followed, ensuring outputs meet standards, and defining a quality plan).

- **Quality assurance vs. quality control.** QA defines the processes and standards that should lead to high-quality products; QC applies those processes to weed out sub-standard products. Both are part of quality management.
- **Independence.** The QM team should be independent of development and report above the project-manager level, so quality goals are not sacrificed to short-term budget/schedule pressure. (In small companies this is impractical, so roles overlap.)

**Why software quality differs from manufacturing quality.** Quality cannot be defined simply as conformance to a complete specification, because: it is hard to write complete, unambiguous requirements; specifications are compromises across stakeholder groups (excluded stakeholders may judge the system poor quality even if it meets the agreed spec); and some quality characteristics (e.g., maintainability) cannot be measured directly. Assessment is therefore partly subjective. The subjective quality of a system largely rests on its **non-functional** attributes (dependability, usability, efficiency, maintainability) - if functionality is wrong, users work around it; if the software is unreliable or slow, they cannot achieve their goals at all.

**Quality plan structure** (Humphrey): product introduction (description, market, quality expectations); product plans (release dates, responsibilities, servicing); process descriptions; quality goals (with justification of critical attributes); and risks and risk management.

**Standards.** Product standards (coding, documentation) and process standards. Make standards work by involving engineers in selecting them (with rationale), reviewing and updating them as technology changes, and providing tool support so conformance is not tedious manual work. Project managers should be allowed to adapt standards to circumstances without losing quality. **ISO 9001** is a framework for developing quality-management standards (not a software standard itself); it requires documenting how organizational processes relate to nine core processes and keeping records that processes were followed. Certification proves procedures exist and are followed, not that the software is better than an uncertified competitor's, because it defines quality as conformance to standards rather than as experienced by users.

**Reviews and inspections.** Quality-assurance activities that check deliverables (software, documentation, process records) for errors, omissions, and standards violations, without executing the software (so incomplete versions and models can be checked). The review process has three phases: pre-review (planning and individual preparation), the review meeting (an author walks the team through the artifact; short, two hours at most; chaired, with a scribe recording decisions), and post-review (fix the issues; the chair verifies follow-up). Purpose is to improve quality, not assess individuals; managers must build a no-blame culture. **Program inspections** (Fagan/IBM) are peer reviews with defined roles (moderator, reader, scribe, author) doing a line-by-line review against a checklist of common errors.

**Quality management and agile.** Traditional QM relies on extensive documentation and is at odds with agile's minimal-documentation aim, so QM evolves into a **team quality culture**: reviews, automated testing, and refactoring built into iterations; quality discussed each iteration; a sprint may focus on refactoring/quality rather than new features. Code review may be per-individual (check before check-in) or via pair programming. Pair programming gives deep program knowledge but the pair cannot be as objective as an external team (risks: mutual misunderstanding, pair reputation, reluctance to criticize a close partner). For large external-customer systems, some formal quality documentation and processes usually have to be reintroduced.

**Software measurement.** Quantifying an attribute (e.g., complexity, reliability) to compare against standards and assess process/tool effectiveness. A metric is an objectively measurable characteristic of a system, its documentation, or its process (e.g., lines of code, Fog readability index, reported faults, person-days). **Control (process) metrics** support process management (time, resources, occurrences of events such as defects found in inspection). **Predictor (product) metrics** help predict software characteristics (cyclomatic complexity, identifier length, attribute/operation counts). Both feed management decisions. Fully automated quality assessment from metrics remains unrealistic.

## Formal specification and verification (Alagić)
A complementary, more mathematical view of the specification-development-validation activities:
- **Modeling.** System analysis identifies actors, use cases (ways of using the system), and information requirements; system design produces an implementation-independent model of the application environment using a modeling framework (UML structural/entity diagrams, sequence diagrams for behavior, use cases tying them together). The model must be abstract enough to survive change yet implementable.
- **Design by contract / assertions.** Specifications are expressed as **class invariants** (conditions that must always hold for objects of a class), **preconditions** (what must hold before a method runs), and **postconditions** (what the method guarantees on completion). Assertions interact with inheritance and can be defined for interfaces. Constraints over collections, type conformance and subtyping, static vs. dynamic type checking, and dynamic binding all bear on whether a specification is satisfied.
- **Verification.** Enforcing declarative specifications (OCL-style) in procedural code, ideally **statically** (inspecting annotated code to prove it meets the spec) and otherwise **dynamically** (checking assertions as code executes). Alagić illustrates with Microsoft's Code Contracts in C#, where assertions appear as calls to a `Contract` class (`Contract.Requires(...)` for preconditions, `Contract.Ensures(...)` for postconditions), giving dynamic checking with some static capability. Transactions can be verified against their pre/postconditions.

## Professional and ethical practice
Software engineering is carried out within a social and legal framework; engineers have responsibilities beyond technical skill. Areas of professional responsibility (not all bound by law): **confidentiality** (respect employer/client confidentiality), **competence** (do not misrepresent your skill or accept work beyond it), **intellectual property rights** (respect patents and copyright), and **computer misuse** (do not misuse others' computers).

**ACM/IEEE-CS Software Engineering Code of Ethics - eight principles:** (1) PUBLIC - act consistently with the public interest; (2) CLIENT AND EMPLOYER - act in their best interests, consistent with the public interest; (3) PRODUCT - meet the highest professional standards; (4) JUDGMENT - maintain integrity and independence of professional judgment; (5) MANAGEMENT - promote an ethical approach to managing development and maintenance; (6) PROFESSION - advance the integrity and reputation of the profession; (7) COLLEAGUES - be fair to and supportive of colleagues; (8) SELF - engage in lifelong learning and promote an ethical approach to practice. Ethical dilemmas (disagreement with management, when to reveal project problems, an employer acting unethically, participation in military/nuclear systems) rarely have absolute answers; weigh the potential for damage, its extent, and who is affected, and try to resolve issues while respecting the employer's rights.

## Patterns
- **Holistic sociotechnical design** - *Intent:* design hardware, software, and operational processes together so faults do not accumulate at interfaces. *When:* any dependable, software-intensive system. *Trade-offs:* broader analysis effort; requires cross-disciplinary collaboration.
- **Redundancy and diversity for fault tolerance** - *Intent:* tolerate faults by duplicating components and providing alternative ways to do the same thing. *When:* availability/reliability/safety are critical. *Trade-offs:* performance cost from extra checking; higher development cost.
- **Independent quality management** - *Intent:* keep an objective check on quality by separating QM from development and reporting above the project manager. *When:* large, long-lifetime, externally contracted systems. *Trade-offs:* overhead; impractical for small teams.
- **Reviews and inspections** - *Intent:* find defects early and cheaply without executing code, including on models and incomplete artifacts. *When:* throughout development; especially valuable for critical artifacts. *Trade-offs:* time to organize; must maintain a no-blame culture.
- **Design by contract** - *Intent:* make behavior precise and checkable via invariants and pre/postconditions, enabling static or dynamic verification. *When:* correctness must be demonstrable; complex object models. *Trade-offs:* specification effort; static verification needs language/tool support.

## Principles & best practices
- Treat software as programs plus all the documentation, configuration, and support needed to make them useful.
- Engineering is selecting the most appropriate method for the circumstances and accepting compromise; do not seek perfection.
- Declare the most important quality attributes up front; you cannot optimize all of them.
- Dependability usually matters more than detailed functionality, because failures affect many users, cost can be enormous, and users abandon untrustworthy systems.
- Build dependability in through redundancy, diversity, fault tolerance, and effective V&V, not as an afterthought.
- Keep quality plans short enough that people actually read them.
- Run reviews and inspections to improve quality, never to judge individuals; protect a no-blame culture.
- Uphold the eight-principle code of ethics; weigh public safety and harm above narrow technical or contractual concerns.

## Anti-patterns & pitfalls
- **Equating quality with conformance to standards** (the ISO 9001 trap): you can follow defined procedures and keep records while still shipping poorly tested, low-quality software.
- **Optimizing software or hardware in isolation**, ignoring the operational/human element, so failures cluster at interfaces.
- **Trading away dependability checks for performance** without recognizing the consequential risk that an undetected fault causes failure.
- **Over-trusting metrics**: expecting automated, objective quality verdicts that current measurement cannot deliver.
- **Abandoning all documentation under agile** for large, long-lifetime, externally contracted systems, leaving new team members unable to understand decisions or assess change impact.
- **Misrepresenting competence** or accepting work beyond one's ability - an ethical and a quality failure.

## Decision guidance
- Identify the application type first; it is the strongest signal for which methods and quality attributes matter.
- Rank the five dependability properties for the specific system (an insulin pump ≠ a patient records system ≠ a weather station) and concentrate effort there; do not pay for dependability the system does not need.
- Match quality-management formality to context: lightweight team quality culture for product development the company controls; formal quality plans, reports, and documentation when the customer is a large or regulated organization or when teams are distributed or the system is long-lived.
- Reach for formal specification and verification (design by contract, model-driven development) when correctness must be provable or the consequences of failure are severe; accept the added specification effort and tooling needs.
- When facing an ethical dilemma, assess the potential damage, its extent, and who is affected; prefer resolving from within while respecting the employer, escalating only as the danger warrants.

## Source attribution
- Definition of software engineering, attributes of good software, product types, diversity, cross-cutting challenges, relationship to CS/systems engineering, and the four process activities: Sommerville, *Software Engineering, Global Edition*, 10th ed., 2021, chapter 1.
- Software engineering ethics and the ACM/IEEE eight-principle code: Sommerville, chapter 1, section 1.2.
- Dependability and security (five properties, redundancy/diversity, sociotechnical systems, cost of dependability): Sommerville, chapter 10.
- Quality management (QA/QC, quality plan, standards, ISO 9001, reviews and inspections, agile quality, measurement/metrics): Sommerville, chapter 24.
- Formal specification, design by contract (invariants, pre/postconditions), and verification (Code Contracts, static/dynamic checking): Alagić, *Software Engineering: Specification, Implementation, Verification*, 2017, chapters 1, 2, 6.

## Recent developments (2024–2026)
The fundamentals (process, dependability, requirements, management, reuse, ethics) remain stable, but two forces have reshaped how they play out in practice: pervasive AI-assisted development and a new wave of software-specific regulation.

- **AI-generated code is now a dependability and quality concern, not just a productivity story.** AI adoption among developers reached roughly 90% in 2025, but multiple 2025-2026 studies report that AI-generated code carries materially more defects and vulnerabilities than human-written code. Veracode's 2025 GenAI Code Security Report found AI-generated code contained about 2.74x more vulnerabilities and failed secure-coding benchmarks roughly 45% of the time (SQL injection, hardcoded credentials, weak crypto). This validates Sommerville's argument that dependability and V&V must be built in, not bolted on: AI shifts more of the quality burden onto reviews, automated testing, and security scanning. (Veracode, 2025, veracode.com/blog/ai-generated-code-security-risks; Dark Reading, 2025)
- **AI-driven technical debt and code bloat.** Large-scale empirical studies in 2025-2026 found AI-accelerated coding increases changeset size and accumulates code smells, maintainability issues, and security flaws when accepted unscrutinized. The average developer checked in roughly 75% more code in 2025 than in 2022, amplifying the maintainability attribute Sommerville flags as central. "Vibe coding" (accepting AI output with minimal review) is now a recognized anti-pattern that raises total cost of ownership. (LeadDev, 2025; arXiv "Debt Behind the AI Boom," 2026)
- **The EU AI Act (Regulation (EU) 2024/1689)** entered into force on 1 August 2024, with phased application: prohibitions from February 2025, general-purpose AI model obligations from August 2025, and high-risk system obligations by August 2026. High-risk AI systems face strict requirements on risk assessment, data quality, documentation, transparency, human oversight, and accuracy, directly extending Sommerville's "prove safety to an external regulator" point to AI-intensive systems. (European Commission, digital-strategy.ec.europa.eu, 2024-2025)
- **Software is now a "product" under EU product liability.** The revised EU Product Liability Directive (in force 8 December 2024) explicitly brings software and AI systems under strict liability, and treats cybersecurity vulnerabilities and unpredictable AI behavior as potential defects, meaning "the AI acted unexpectedly" is not a defense. This sharpens the professional and ethical stakes around dependability and competence. The standalone AI Liability Directive proposal was withdrawn in October 2025, leaving the PLD as the primary liability framework. (Pinsent Masons / Out-Law, 2024; Norton Rose Fulbright, 2025)
- **Quality management is shifting toward AI-augmented review and measurement.** AI is increasingly used inside reviews and inspections (automated code review, ambiguity detection, requirements quality scoring). The Qodo 2025 AI Code Quality report noted AI code review raising measured quality-improvement rates, and tooling now scores artifacts against standards automatically, though human judgment and the no-blame review culture remain essential. This is consistent with Sommerville's caution that fully automated quality verdicts remain out of reach. (Qodo, 2025)
- **Resilience and software supply-chain security are rising in prominence** alongside the original five dependability properties, driven by high-profile supply-chain incidents and regulation (the PLD's cybersecurity-defect provisions, plus SBOM and secure-by-design expectations), reinforcing resilience as a first-class attribute rather than a recent add-on.
