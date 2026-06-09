# SDLC and Process Models

**Domain:** 01 - Foundations and SDLC Process
**Primary sources:** Software Engineering, Global Edition, 10th ed. (Sommerville, 2021), chapters 1-3; Software Engineering: Specification, Implementation, Verification (Alagić, 2017); Engineering Software Products (Sommerville, 2020).

## Overview
A software process is a set of related activities that leads to the production of a software system. A software process model (also called a Software Development Life Cycle, or SDLC, model) is a simplified, abstract representation of such a process from a particular perspective. There is no universal process that fits all software: the right model depends on the type of software, the customer and regulatory context, the operating environment, and the skills of the team. The central decision in process selection is how much to plan up front versus how much to defer and adapt, that is, where to sit on the spectrum between plan-driven and agile approaches.

Choosing and shaping the process well matters because mistakes propagate. Requirements errors found after deployment cost far more to fix than the same errors caught during specification (Sommerville cites roughly 60% of software cost as development and 40% as testing for new systems, with evolution costs often exceeding initial development cost for custom software; Koelsch cites the classic figure that fixing a requirement defect after deployment can cost 100 to 200 times more than fixing it during requirements definition). The process is the main lever for catching problems early and for absorbing the change that is inevitable in any non-trivial project.

## Core concepts

**The four fundamental process activities** (common to every software process, however organized):
1. **Software specification** - customers and engineers define what the software must do and the constraints on its operation (requirements engineering).
2. **Software development** - the software is designed and programmed (design plus implementation).
3. **Software validation** - the software is checked (verified and validated) to ensure it does what the customer needs.
4. **Software evolution** - the software is modified to reflect changing customer and market requirements.

In the waterfall model these run in sequence; in incremental and agile models they are interleaved.

**Process description elements.** When you describe a process, describe not just the activities and their ordering but also: **products/deliverables** (outcomes of an activity, e.g., an architecture model), **roles** (responsibilities, e.g., project manager, configuration manager, programmer), and **pre- and post-conditions** (conditions that must hold before an activity starts and after it produces a product).

**Plan-driven vs. agile.** In a plan-driven process all activities are planned in advance and progress is measured against the plan. In an agile process planning is incremental and continual, so the process more easily reflects changing requirements. Boehm and Turner's key point: each suits different software, and for large systems you usually need a balance of the two.

**Change anticipation vs. change tolerance.** Two complementary ways to reduce the cost of rework. *Change anticipation* predicts likely changes before significant rework is needed (e.g., prototyping). *Change tolerance* designs the process and software so changes are cheap to make (e.g., incremental development plus refactoring).

## Frameworks & models

### Waterfall (plan-driven, sequential)
The first published process model (Royce, 1970), derived from large military systems engineering. Phases cascade: (1) requirements analysis and definition, (2) system and software design, (3) implementation and unit testing, (4) integration and system testing, (5) operation and maintenance. Each phase ideally ends in approved ("signed-off") documents; the next should not start until the previous finishes.
- **When to use:** embedded systems where software must interface with fixed hardware; critical systems needing extensive up-front safety/security analysis; large systems built by several partner companies who need complete specifications to develop subsystems independently.
- **Trade-offs:** inflexible to change; tends to cause premature freezing of requirements; problems get programmed around rather than fixed. Not suitable where requirements change quickly or where informal team communication is feasible.
- **V-model variant:** turns the waterfall on its side so each development phase is paired with a corresponding test phase (component design ↔ component test, system design ↔ system integration test, requirements ↔ acceptance/customer test). Test plans are the link between development and testing.
- **Formal development variant:** a mathematical specification is created and refined through consistency-preserving transformations into executable code (e.g., the B method). Used only for systems with stringent safety, reliability, or security requirements because of high cost.

### Incremental development (the dominant modern approach)
Build an initial implementation, get feedback, and evolve through several versions. Specification, development, and validation are interleaved with rapid feedback. Early increments deliver the most important or urgent functionality. Can be plan-driven (increments fixed in advance), agile (later increments depend on progress and priorities), or a mix.
- **Advantages over waterfall:** lower cost of accommodating change; easier to get customer feedback on working software; early delivery of useful software.
- **Problems:** reduced process visibility for managers (fewer documents to measure progress); structural degradation of the code as increments accumulate, countered by regular **refactoring**. For large, long-lifetime, multi-team systems a stable architecture must still be planned up front.

### Integration and configuration (reuse-oriented)
Relies on a base of reusable components or systems plus an integrating framework. Steps: (1) requirements specification (outline), (2) software discovery and evaluation, (3) requirements refinement against what is available, (4) application system configuration, (5) component adaptation and integration. Reuses stand-alone application systems (configured for context), component/package collections (e.g., Spring), and web services.
- **Trade-offs:** less software to build, so lower cost and faster delivery, but requirements compromises are inevitable and you lose control over evolution of components you do not own.

### Spiral model (Boehm, risk-driven)
Represents the process as a spiral rather than a sequence. Each loop is a process phase (feasibility, requirements, design, etc.) and includes explicit risk-management activities. Combines change avoidance with change tolerance, treating changes as a result of project risks.

### Rational Unified Process (RUP)
A "universal" hybrid that brings together elements of all the general models and supports prototyping and incremental delivery. Described from three perspectives: dynamic (phases over time), static (process activities), and practice (good practices). Phases: **inception** (establish the business case), **elaboration** (develop requirements and architecture), **construction** (implement), **transition** (deploy). Flexible but never gained widespread adoption.

### Agile (see agile-foundations.md for depth)
Incremental methods with small increments (new releases every few weeks), interleaved specification/design/implementation, close customer involvement, minimal documentation, and heavy tool support. Best for product development and for in-house custom systems with a committed, available customer and few external regulatory constraints. Scrum (sprint-based project framework), XP (engineering practices: user stories, pair programming, TDD, refactoring, continuous integration, small releases), and Kanban are the common instantiations.

### DevOps and continuous delivery (see modern-product-engineering.md for depth)
Integrates development, deployment, and support into one multi-skilled team. Three principles: everyone is responsible for everything; everything that can be automated should be automated; measure first, change later. Underpins a continuous-integration / continuous-delivery / continuous-deployment pipeline as the modern operational layer over an incremental/agile process.

### Model-driven and design-by-contract development (Alagić)
A more formal route through the lifecycle: system analysis identifies **actors** and their **use cases** (ways of using the system) and the **information requirements** of each activity; system design produces an implementation-independent **model** of the application environment using a modeling framework (Alagić uses UML structural/entity diagrams, sequence diagrams for behavior, and use cases). The model is then refined into an implementation. Specifications are expressed as **class invariants, preconditions, and postconditions** (design by contract) and can be verified statically or dynamically (Alagić demonstrates with Microsoft's Code Contracts in C#). This angle treats specification, implementation, and verification as a continuum and is most relevant where correctness must be demonstrable.

## Process activities in detail

**Specification (requirements engineering).** Three sub-activities: requirements elicitation and analysis; requirements specification (user requirements = abstract statements for customers; system requirements = detailed functional descriptions for developers); and requirements validation (realism, consistency, completeness). A feasibility study may precede it. In agile methods this is not a separate phase but is folded into development.

**Design and implementation.** Design activities (for information systems) include architectural design, database design, interface design, and component selection/design; they are interleaved and interdependent, so rework is inevitable. The platform (OS, database, middleware, other systems) is a key design input. Programming follows design; debugging (locating and correcting defects) is distinct from testing (establishing that defects exist).

**Validation (verification and validation, V&V).** Three staged levels: component testing (by developers), system testing (integration, emergent properties, functional and non-functional requirements), and customer/acceptance testing (with real customer data; called beta testing for products). Testing is iterative, feeding information back to earlier stages.

**Evolution.** The historical split between "development" and "maintenance" is increasingly irrelevant; treat software engineering as a continuum where software is continually changed over its lifetime: assess existing systems, propose changes, modify, repeat.

## Coping with change (techniques)
- **Prototyping** - build an early version quickly to demonstrate concepts, explore design options, and elicit/validate requirements. Make the prototype's objective explicit; decide what to leave out (relax non-functional requirements, error handling, quality) to keep it cheap and fast. A method of change anticipation. Prototypes should not normally become production systems.
- **Incremental delivery** - deliver increments into the customer's real working environment, highest-priority services first. Supports both change avoidance and change tolerance and means the most important services get the most testing. Problematic when replacing an existing system (users want full functionality), when common facilities are hard to identify early, and when procurement contracts demand a complete up-front specification.

## Process improvement
Two contrasting philosophies:
1. **Process maturity approach** - improve process and project management and embed good engineering practice. The SEI **Capability Maturity Model** defines five levels: (1) Initial, (2) Managed, (3) Defined, (4) Quantitatively managed, (5) Optimizing. Now used mainly by large companies; too heavyweight for small ones and hard to reconcile with agile.
2. **Agile approach** - reduce process overhead, deliver functionality rapidly, respond to change; the best process is the one with the lowest overhead.

The maturity-style improvement cycle is itself iterative: **measure → analyze → change**, repeated continuously because the business environment keeps changing.

## Patterns
- **Incremental delivery** - *Intent:* deliver value early and absorb change by shipping prioritized increments into real use. *When:* new systems, available/engaged customers, changing requirements. *Trade-offs:* reduced process visibility; risk of architectural degradation without refactoring; awkward when replacing an existing system or under fixed-spec contracts.
- **Prototyping for change anticipation** - *Intent:* surface requirements and design risks cheaply before committing to production cost. *When:* unclear requirements, novel UI, feasibility unknown. *Trade-offs:* prototype usage may not match real usage; temptation to ship the prototype as production.
- **Hybrid plan-driven + agile** - *Intent:* keep up-front architecture, requirements, and documentation where regulation/scale/criticality demand it, while using agile engineering practices in construction. *When:* large, regulated, multi-team, or long-lifetime systems. *Trade-offs:* more overhead than pure agile; requires cultural alignment.
- **Reuse-oriented integration and configuration** - *Intent:* assemble systems from existing components/services to cut cost and time. *When:* suitable components/COTS/web services exist. *Trade-offs:* requirements compromises; loss of control over component evolution.
- **Risk-driven spiral loops** - *Intent:* drive each phase by explicit risk assessment and reduction. *When:* high-uncertainty or high-risk projects. *Trade-offs:* requires disciplined risk-management skill; heavier than agile.

## Principles & best practices
- No universal process exists; select and adapt the model to the type of system, the customer, regulation, and team skills.
- Whatever model you use, build in activities to cope with change.
- For large systems, combine the best features of several models; develop well-understood, stable parts plan-driven (or buy them off the shelf) and hard-to-specify parts incrementally.
- Plan for evolution from the start: most systems spend most of their life being changed.
- Catch requirements and design problems as early as possible; the cost of fixing them rises steeply downstream.
- Refactor continuously under incremental development to prevent structural decay.
- Choose pragmatically: the buyer cares about working software that meets their needs, not whether the label is "agile" or "plan-driven."

## Anti-patterns & pitfalls
- **Premature freezing of requirements** (a waterfall failure mode): problems get worked around rather than fixed, producing badly structured systems.
- **Using a prototype as the production system** without re-engineering for reliability, performance, and quality.
- **Incremental development without refactoring**, leading to messy, increasingly costly-to-change code and degraded structure.
- **Forcing a pure agile approach onto large, regulated, multi-team, or safety-critical systems** where up-front design, documentation, and regulatory artifacts are required.
- **Applying heavyweight plan-driven overhead to small/medium business systems**, where process work dominates and the original reason for the software may change before delivery.
- **Treating maintenance as a lesser, separate process** rather than a continuum, which leads to under-investment and loss of the requirements knowledge needed to assess change impact.

## Decision guidance
To choose where to sit on the plan-driven ↔ agile spectrum, weigh three groups of factors:
- **System:** size (small co-located vs. large multi-team), type (real-time/critical needs up-front analysis), expected lifetime (long life argues for more documentation), and external regulation (regulator approval demands detailed documentation and a safety case).
- **Team:** skill level and distribution (distributed/outsourced work needs design documents to communicate), and available tooling (weak tools argue for more documentation).
- **Organization:** contractual need for a detailed up-front specification, feasibility of incremental delivery with engaged customers, and cultural readiness for informal, low-documentation work.

Heuristics: safety-critical or hardware-coupled → waterfall/formal. Software products and apps → incremental/agile, almost always with continuous delivery. Business systems with changing requirements → incremental, mostly agile. Reusable components/COTS available → integration and configuration. Large systems → hybrid with up-front architecture and requirements, agile construction inside teams, releases aligned across teams. High uncertainty/risk → spiral with explicit risk loops. Correctness must be provable → model-driven, design-by-contract, formal methods.

## Source attribution
- Process models, the four fundamental activities, waterfall/incremental/integration-and-configuration, V-model, spiral, RUP, prototyping, incremental delivery, change anticipation vs. tolerance, CMM levels, and process improvement: Sommerville, *Software Engineering, Global Edition*, 10th ed., 2021, chapters 1-2.
- Agile vs. plan-driven distinction and factor-based selection: Sommerville, *Software Engineering, Global Edition*, chapter 3.
- DevOps/continuous delivery as the modern operational layer: Sommerville, *Engineering Software Products*, 2020, chapter 10.
- Model-driven analysis/design, use cases, design by contract (invariants, pre/postconditions), and the specification-implementation-verification continuum: Alagić, *Software Engineering: Specification, Implementation, Verification*, 2017, chapters 1, 2, 6.
- Cost-of-fixing-requirements escalation figure: Koelsch, *Hardware and Software Projects Troubleshooting*, 2nd ed., 2024, chapter 1.

## Recent developments (2024–2026)
The core process models are stable, but AI has emerged as a cross-cutting layer over every process activity, and a new "spec-driven" process pattern has appeared specifically to govern AI-assisted construction.

- **The "AI-native" SDLC.** Industry framing in 2025-2026 describes AI moving from code-completion into every process activity (specification, design, development, validation, evolution), with reported productivity gains of roughly 25-30% when AI is applied across the full lifecycle versus only 10-15% when limited to code generation. This does not replace the four fundamental activities; it interleaves AI agents into each. (EPAM, 2025, epam.com/insights/ai; Master of Code, 2025)
- **Agentic SDLC and the new process bottleneck.** Agentic tools (Claude Code, OpenAI Codex CLI, Google Jules, Devin, OpenHands, SWE-agent) now operate at the granularity of a repository, feature, or algorithm rather than a single line, prompting reference architectures for "agentic SDLC" that orchestrate multi-step builds. As with the original DevOps motivation, automating one activity exposes the next constraint: review, verification, and integration of large AI-generated changesets become the limiting factor. (Microsoft Community Hub, 2025; arXiv "Agentic AI in the SDLC," 2026; Augment Code, 2025)
- **Spec-driven development (SDD) as a new lifecycle pattern.** A 2025 movement reframes the specification as the executable source of truth that AI agents use to generate, test, and validate code. GitHub's open-source Spec Kit (Spec to Plan to Tasks to Implement, each a Markdown artifact) and Amazon's Kiro IDE (requirements.md in EARS format, design.md, tasks.md) operationalize this. It is in effect a disciplined, plan-light front end bolted onto agentic construction, partially reversing the agile "minimal up-front specification" stance because AI works better against explicit specs. (GitHub Blog, 2025, github.blog; Martin Fowler, 2025, martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html; Kiro, 2025)
- **DORA's pivot to AI and away from tiered ranking.** The 2025 DORA report (retitled "State of AI-Assisted Software Development") reports ~90% AI adoption and finds AI's effect on organizational performance depends heavily on platform quality and existing process stability: AI amplifies chaos in unstable systems but acts as leverage for high performers. DORA replaced its long-standing four-tier Elite/High/Medium/Low model with seven team archetypes blending delivery metrics with human factors (burnout, friction, perceived value). The four key metrics remain. (DORA / Google Cloud, 2024-2025, dora.dev)
- **Hybrid process models dominate in practice.** The 18th State of Agile Report (2025) found 74% of organizations now use hybrid, blended, or homegrown models rather than a single named method, reinforcing Sommerville's "combine the best features of several models" guidance over methodological purity. (Digital.ai, 2025)
- **Platform engineering as the operational substrate.** By 2025, internal developer platform adoption was near-universal (~90%), and DORA found AI's performance benefits are strong only where platform quality is high, making the platform layer a precondition for getting value from an AI-accelerated process. (DORA, 2025; dora.dev/capabilities/platform-engineering)
