# Agile Foundations

**Domain:** 01 - Foundations and SDLC Process
**Primary sources:** Becoming Agile in an Imperfect World (Smith & Sidky, 2009, Manning); Software Engineering, Global Edition, 10th ed. (Sommerville, 2021), chapter 3; Engineering Software Products (Sommerville, 2020), chapter 2.

## Overview
Agile is not just another process or a fixed set of practices - it is a **mindset**, a way of thinking about software development that can be applied to any process using any set of practices. Smith and Sidky frame it as three concentric layers: agile **values** at the core (what adds value), surrounded by agile **principles** (the inherent traits of an agile process), surrounded by agile **practices** (the concrete activities that implement the principles and values). The point of the values is to make a process **light** - increase everything that adds value to the end goal and decrease everything that does not - so the organization can respond to constant market change and seize opportunities before competitors.

The central business case for agile (Smith & Sidky's framing): software has value only while the need still exists, like a Sunday newspaper delivered on Monday. Agile asks you to identify what is critical and deliver it as soon as possible, to expect and embrace change, and to work collaboratively with customers and vendors as partners rather than adversaries. Sommerville's complementary framing: rapid software development and delivery is the most critical requirement for most business systems, and plan-driven processes that fully specify before building are too slow for fast-moving environments.

Crucially, Smith and Sidky's book is about adopting agile in an **imperfect** organization - one that already has legacy practices, bureaucracy, and constraints. Its core contribution is a **readiness-assessment** approach and the **Sidky Agile Measurement Index (SAMI)** for introducing agile incrementally, by value, rather than copying a methodology wholesale.

## Core concepts

**The Agile Manifesto - four values.** "We are uncovering better ways of developing software by doing it and helping others do it. Through this work we have come to value:
- Individuals and interactions **over** processes and tools
- Working software **over** comprehensive documentation
- Customer collaboration **over** contract negotiation
- Responding to change **over** following a plan
That is, while there is value in the items on the right, we value the items on the left more."
A common misreading is that agile throws away the right-hand items. It does not: those items still add value; agile says the left-hand items add *more*, and that organizations have traditionally over-emphasized the right and neglected the left. An agile process can and sometimes should include items on the right - but each must add indispensable value.

**The twelve agile principles** (Manifesto, as explained by Smith & Sidky):
1. Satisfy the customer through early and continuous delivery of valuable (working) software - not prototypes or documents.
2. Welcome changing requirements, even late, for the customer's competitive advantage (change is welcome but not free).
3. Deliver working software frequently (weeks, not months; prefer the shorter timescale) to get feedback early.
4. Business people and developers work together daily (often via business proxies - analysts, product/program managers).
5. Build projects around motivated individuals; give them environment, support, and trust (people are not "resources"; software is more art than manufacturing).
6. Face-to-face conversation is the most efficient and effective communication (email/IM lose context and add ambiguity).
7. Working software is the primary measure of progress (value-driven: 30% complete = 30% coded, integrated, tested, deployed - not 30% of the plan).
8. Promote sustainable development; sponsors, developers, and users maintain a constant pace indefinitely (effort distributed evenly, not crunched at the end).
9. Continuous attention to technical excellence and good design enhances agility (extensible designs, automated testing, continuous integration are enablers).
10. Simplicity - the art of maximizing work not done - is essential (no code means no bugs; do not build what is not needed now).
11. The best architectures, requirements, and designs emerge from self-organizing teams (give the team the task without prescribing who does what).
12. At regular intervals the team reflects and tunes its behavior (Smith & Sidky call this the most important principle - the essence of continuous improvement).

**Agile practices** are activities that manifest the principles and values (user stories, TDD, pair programming, daily stand-ups, collaborative planning, refactoring, continuous integration, etc.). There is no fixed, defined set - claiming one exists is anti-agile. Organizations tailor existing practices and invent new ones to fit their needs while adhering to the values and principles.

## Frameworks & models

### Scrum (project-organization framework)
The most widely used agile method; provides a framework for organizing agile projects and some external visibility, without mandating engineering practices (so it integrates with existing company practice). Deliberately new terminology distances it from conventional project management.

**Roles:** the **Development Team** (self-organizing, ≤7 people, responsible for the software and essential documents; ideal product-development team is five to eight with diverse expertise); the **Product Owner** (identifies and prioritizes product features/requirements, continuously reviews the backlog to keep it aligned with business needs; may be a customer or product manager); the **ScrumMaster** (ensures the Scrum process is followed, guides the team, shields it from outside interference, interfaces with the company - explicitly not a project manager, though in practice often takes that role).

**Artifacts and concepts:** the **Product Backlog** (prioritized to-do list of features, requirements, user stories, and supporting tasks such as architecture or documentation); the **Sprint Backlog** (work selected for a sprint, broken into short tasks of one-two days); the **Sprint** (a timeboxed development iteration, usually two-four weeks, never extended - unfinished items return to the product backlog); **velocity** (how much backlog effort a team covers per sprint, used to plan and measure improvement); the **potentially shippable product increment** (the sprint output, in a finished state needing no further work to ship); the **Scrum/daily stand-up** (short daily meeting to share progress, surface problems, re-plan the day - no top-down direction); and the **Scrum board** (shared whiteboard of sprint backlog, work done, availability).

**Sprint cycle:** review/prioritize the product backlog → select highest-priority items the team believes it can complete and estimate them → create the sprint backlog and self-organize → execute with daily scrums → produce the increment → hold a **sprint review** (assess whether the goal was met, surface issues, and reflect on how to improve - a built-in process review for continuous improvement). Backlog estimation uses **effort (person-hours/days)** or **story points** (relative, abstract estimates accounting for size, complexity, technology, and unknowns, benchmarked against a baseline task; Planning Poker is the recommended team estimation technique). A **definition of done** (e.g., reviewed, unit tested, integrated, integration tested, accepted) sets what "complete" means.

**Distributed and scaled Scrum:** Scrum was designed for co-located teams. Distributed Scrum needs videoconferencing between Product Owner and team, the ScrumMaster located with the team, a Product Owner who visits and builds trust, a common development environment, continuous integration, and real-time informal communication (IM/video). Multi-team Scrum uses **role replication** (a Product Owner and ScrumMaster per team, plus chief roles), **product architects** (one per team, collaborating on overall architecture), **release alignment** (aligned release dates), and the **Scrum of Scrums** (daily meeting of team representatives).

### Extreme Programming (XP) - engineering practices
XP (Beck) pushed recognized good practice to "extreme" levels and contributed the engineering practices most teams adopt, often layered under Scrum:
- **User stories / story cards** - requirements as scenarios, broken into tasks, prioritized by the customer for ~two-week releases.
- **Refactoring** - continuously improve code structure when an improvement is seen, even without immediate need; counters the structural degradation of incremental change. (XP discards "design for change" as often-wasted effort, relying on refactoring instead.)
- **Test-first / test-driven development (TDD)** - write automated tests before code; tests define the interface and behavior, reduce misunderstanding, and avoid "test-lag." Test automation (e.g., JUnit) is essential.
- **Pair programming** - two developers at one computer; supports collective ownership, acts as continuous informal review, and encourages refactoring. Productivity evidence is mixed (comparable to two individuals for student volunteers; a productivity loss for experienced programmers), but knowledge sharing reduces project risk when people leave.
- Plus **collective ownership, continuous integration, incremental planning, on-site customer, simple design, small releases, sustainable pace.**

### Kanban (essentials)
A pull-based, flow-oriented agile approach (visualize work, limit work-in-progress, manage flow) used to evolve an existing process incrementally; complements Scrum/XP where fixed-length sprints do not fit. (Both Sommerville and Smith & Sidky treat practices as selectable and tailorable rather than prescribing a single method.)

### Sidky Agile Measurement Index (SAMI) - adopting agile by value
The book's framework for moving an organization to agility incrementally and by value, not by copying XP or Scrum. The SAMI is similar in spirit to the CMMI but is **not** a certification or ranking framework - it is a guide for value-driven adoption. It breaks adoption into **five agile levels (steps)**, each representing a core agile value and populated with synergistic practices; you attain a level only by fully adopting its associated practices. The naming/ordering was inspired partly by Gladwell's *Tipping Point* factors (people, message, environment):

1. **Collaborative** - value: enhancing communication and collaboration (people factor; collaboration appears in the Manifesto's 1st and 3rd statements). The first step.
2. **Evolutionary** - value: delivering software early and continuously, because most agile practices depend on evolutionary (not big-bang) development.
3. **Effective / Integrated** - value: developing high-quality, working software in an efficient, integrated manner; an integrated, resilient environment must exist before the team can respond to change without jeopardizing quality.
4. **Adaptive** - value: responding to change through multiple levels of feedback (depends on the integrated level preceding it).
5. **Encompassing** - value: establishing a vibrant, all-encompassing environment that sustains agility.

The SAMI's second component is **five condensed agile principles** (a grouping of the twelve) that guide which practices populate each level: embrace change to deliver customer value; plan and deliver software frequently; human-centric; technical excellence; customer collaboration. The third component is the **~40 agile practices** that populate the levels. The SAMI does not dictate which practice goes at which level - that is the agile coach's judgment (e.g., Cohn puts user stories at level 1 for collaboration; others put pair programming there) - but the placement must adhere to the values and principles. The SAMI guides the adoption process; it does not dictate it.

### Readiness assessment (the "fitness test")
Before adopting a practice, assess whether the team, project, and organization are **ready** for it, rather than rushing to popular practices the organization is not prepared for. Each agile practice has a **readiness-assessment table** that lists **organizational characteristics** to assess (e.g., for collaborative planning: management style, manager buy-in, power distance, developer buy-in) and, for each characteristic, **indicators** (survey questions) targeted at four roles:
- **M** - managers,
- **D** - developers,
- **C** - customers/product owners (team members in direct contact with the customer),
- **A** - assessors (people outside the team - agile coaches, QA, process-improvement staff, independent observers - who observe whether process activities and artifacts exist).
Indicators are answered on a five-point scale (Strongly Disagree → Strongly Agree). For example, "power distance" indicators reveal whether people are intimidated by managers and afraid to be honest in their presence. The results show in which areas the team is ready and in which it is not, so adoption can target the most logical, lowest-risk areas first.

### Adoption journey (Acme Media case-study arc)
Smith & Sidky walk a fictional company through a value-driven adoption: assess readiness → identify existing valuable practices to keep (do not discard legacy practices that add value) → select a **core team** and a **pilot project** → design a development process that fits the specific situation (coaching the team as it matures) → run the pilot (with iteration 0 for foundational setup: environment, funding, vendor/partner contracts) → run development iterations (design/code/refine/test/deliver in ~10 working days, burndown charts, daily meetings, early testing and integration) → adapt during and after iterations (respond to discoveries; customer demonstrations; validate status by measuring working code) → aggregate iterations into releases (decide when quality is "good enough," validate non-functional requirements) → retrospectives → move from project-level to enterprise-level adoption guided by the SAMI.

## Patterns
- **Values → principles → practices layering** - *Intent:* treat agile as a mindset, adopting practices because they serve agile values and principles, not because they are fashionable. *When:* any agile adoption. *Trade-offs:* requires judgment and coaching; slower than copying a named method but more durable.
- **Readiness assessment before adoption** - *Intent:* assess team/project/organization readiness (characteristics + role-targeted indicators) and adopt where ready first. *When:* introducing agile into an imperfect, legacy organization. *Trade-offs:* up-front assessment effort; needs honest answers (power distance can distort them).
- **SAMI value-staged adoption** - *Intent:* introduce agile incrementally by value level (Collaborative → Evolutionary → Effective → Adaptive → Encompassing). *When:* organization-wide or enterprise adoption. *Trade-offs:* practice placement is judgment-based; not a certification, so no external badge.
- **Keep valuable legacy practices** - *Intent:* identify and retain existing practices that add value rather than discarding everything when moving to agile. *When:* migrating an established organization. *Trade-offs:* must honestly evaluate which legacy practices still add value.
- **Pilot project + core team** - *Intent:* prove agile on one project with a successful team that then spreads practice across the organization. *When:* scaling out agile in a large company. *Trade-offs:* slower than top-down mandate, but builds buy-in and reduces risk.
- **Timeboxed sprints** - *Intent:* fixed-length iterations that force prioritization and yield demonstrable progress; never extended. *When:* iterative product/feature development. *Trade-offs:* unfinished work returns to the backlog; needs a stable enough velocity to plan.
- **Hybrid agile + plan-driven scaling** - *Intent:* add up-front requirements, architecture, documentation, common tooling, and aligned releases when scaling agile to large/regulated/distributed work. *When:* large systems, multiple teams, regulators, brownfield environments. *Trade-offs:* more overhead than pure agile; cultural change required (see Disciplined Agile Delivery / Agility at Scale, SAFe).

## Principles & best practices
- Treat agile as a mindset; adopt practices that serve the values and principles, and only keep right-hand-side items (process, tools, documentation, contracts, plans) that add indispensable value.
- Deliver working software early and frequently; measure progress by working software, not by plan completion.
- Welcome change, but remember it is not free; build technical excellence (automated tests, CI, extensible design) so the system can absorb change.
- Build around motivated, trusted, self-organizing teams; favor face-to-face communication.
- Maximize work not done - keep both the product and the process simple.
- Reflect and tune at regular intervals (retrospectives); continuous improvement is the most important principle.
- Adopt agile by assessing readiness first and introducing practices incrementally and by value (SAMI), targeting areas where the team/organization is ready.
- Do not discard legacy practices that still add value.
- Start with a pilot project and a core team; let success spread the practice; introducing agile across a large company is a slow process of cultural change needing evangelists.

## Anti-patterns & pitfalls
- **Using popular agile practices before the team is ready** for them.
- **Adopting practices without embracing the agile values and principles** behind them (cargo-cult agile).
- **Discarding all legacy practices** when moving to agile, including ones that add value.
- **Misreading the Manifesto** as throwing away process, tools, documentation, contracts, and planning.
- **Treating the ScrumMaster as a conventional project manager** giving top-down direction.
- **Extending sprints to finish unfinished work** instead of returning items to the backlog.
- **Forcing pure agile onto large, regulated, distributed, brownfield, or long-procurement systems** without up-front requirements/architecture/documentation and cross-team coordination - agile must be scaled and hybridized for these (Sommerville: systems of systems, brownfield, regulatory constraints, diverse stakeholders, prolonged procurement).
- **Agile in maintenance** is contested: lack of a requirements document makes change-impact assessment hard; keeping customers engaged and the team continuous is difficult.
- **Cultural resistance and incompatible quality/change-management procedures** in large organizations; introducing agile bit by bit from an enthusiastic group works better than a mandate.

## Decision guidance
- Decide agile vs. plan-driven (or the mix) by the system (size, type, lifetime, regulation), the team (skill level, distribution, tooling), and the organization (contractual need for up-front specs, feasibility of incremental delivery with engaged customers, cultural readiness) - see sdlc-and-process-models.md.
- Agile works best for software products/apps and for in-house custom systems with a committed, available customer and few external regulatory constraints, developed by small co-located teams.
- For adoption in an imperfect organization: assess readiness per practice and per role first; keep valuable legacy practices; pilot with a core team; design a process that fits the specific situation; and use the SAMI to introduce agile by value level, scaling from project to enterprise.
- To scale agile to large systems: add up-front requirements and architecture, multiple customer representatives, more documentation, common tooling across teams, aligned releases, Scrum of Scrums, and (where needed) plan-based practices (Disciplined Agile Delivery → Agility at Scale; SAFe).
- For correctness-critical or regulated work, blend agile construction with plan-driven specification and the documentation regulators require.

## Source attribution
- Agile as a mindset; values-principles-practices layering; the business case for agile; the twelve principles; SAMI (five levels, five condensed principles, ~40 practices, value-driven adoption, contrast with CMMI); readiness assessment (characteristics, role-targeted indicators M/D/C/A); keep-valuable-legacy-practices; pilot + core team; the Acme Media adoption arc (iteration 0, development iterations, adapting, releases, retrospectives, enterprise adoption): Smith & Sidky, *Becoming Agile in an Imperfect World*, 2009, intro and chapters 1, 3, 4, 17, 23.
- Agile Manifesto, plan-driven vs. agile distinction, XP practices (user stories, refactoring, TDD, pair programming, continuous integration, etc.), Scrum (roles, artifacts, sprint cycle, velocity), distributed and scaled Scrum, scaling agile (large-project characteristics, Disciplined Agile Delivery/Agility at Scale, SAFe), and agile in maintenance/large organizations: Sommerville, *Software Engineering, Global Edition*, 10th ed., 2021, chapter 3.
- Scrum product backlog grooming, timeboxed sprints, sprint goals, story points/velocity, definition of done, self-organizing teams in a product context: Sommerville, *Engineering Software Products*, 2020, chapter 2.

## Recent developments (2024–2026)
Agile's values, principles, and core frameworks (Scrum, XP, Kanban, SAMI-style staged adoption) remain current. The major 2024-2026 development is AI moving from an assistant to an active participant in agile delivery, plus continued consolidation around hybrid practice.

- **The "Fourth Wave" of software delivery.** Digital.ai's 18th State of Agile Report (2025) frames AI as a turning point where agentic systems become active participants that "reason, decide, and act" in the delivery lifecycle, transforming agile from a team practice into an enterprise-wide capability. AI adoption among agile teams rose from 68% to 84% year over year, with over a quarter of AI-experimenting organizations already using agentic AI for workflow execution, risk detection, and planning. (Digital.ai, 2025, digital.ai/press-releases; meriroos.ee/Stuff/Digital-ai-18th-State-of-Agile.pdf)
- **Governance lags adoption.** The same report found only 49% of organizations have guardrails in place even as AI adoption accelerates, echoing Smith and Sidky's readiness-assessment thesis: adopting practices (now AI practices) before the organization is ready creates risk. (Digital.ai, 2025)
- **Evolving roles, not new frameworks.** In 2025 the Scrum roles are described as expanding: product owners as strategic, data-backed decision makers; scrum masters as organizational change agents; developers as AI-augmented, cross-functional contributors. The frameworks themselves are unchanged; the human roles around them are absorbing AI. (Agile-GAP, 2025; Scrum Alliance 2025 Annual Report)
- **Hybrid is now the norm.** 74% of organizations report using hybrid, blended, or homegrown agile models, with Scrum still most-used and Kanban surging as a co-staple, validating the book's "tailor practices to fit, do not copy a method wholesale" stance over methodological purity. (Digital.ai 18th State of Agile, 2025)
- **ROI pressure reshapes agile investment.** 76% of respondents say pressure to demonstrate ROI is reshaping investment and 79% report being asked to "do more with less," sharpening the agile value-focus (deliver what is critical first) under tighter economic constraints. (Digital.ai, 2025)
- **AI changes how change is welcomed.** Faster AI-assisted implementation lowers the cost of late change (agile principle 2), but larger AI-generated changesets raise integration and quality risk (DORA 2025), reinforcing principles 9 and 10 (technical excellence, simplicity) as the enablers that keep agility safe.
