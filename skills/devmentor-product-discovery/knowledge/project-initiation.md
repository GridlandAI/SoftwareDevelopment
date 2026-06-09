# Project Initiation

**Domain:** 03 - Product Discovery and Planning
**Primary sources:** Practical Project Initiation: A Handbook with Tools (Karl E. Wiegers, 2007, Microsoft Press)

## Overview
Project initiation is the process of formally conceiving, approving, and launching a project. Wiegers' thesis is that the time and thought invested up front "lays the groundwork for all the work that follows," and that seasoned managers know attending to a handful of initiation activities - defining success, identifying stakeholders, chartering, surfacing risks, negotiating realistic commitments - is what separates success from failure. The book is deliberately practical: most chapters end with worksheets and templates.

Wiegers also argues there is no such thing as "project management" as a single thing; it is the composite management of people, requirements, commitments, resources, change, risks, opportunities, expectations, technology, suppliers, and conflicts. The handbook applies to any life cycle (including agile) and to non-software projects, adapted to size.

## Core concepts

- **Project charter.** The key initiation deliverable: a short document that ensures stakeholders share a common understanding of where the project is headed. It is not a contract and not a project plan; it is a high-level alignment artifact.
- **Success criteria.** Specific, measurable, trackable conditions derived from business objectives that define what "success" means - beyond just hitting a schedule.
- **Drivers, constraints, and degrees of freedom.** Every project balances five dimensions - features, staff, cost, schedule, quality. Each must be classified as a *constraint* (a fixed boundary), a *driver* (strongly tied to success), or a *degree of freedom* (adjustable). Not all five can be constraints; an over-constrained project has no way to absorb change and will likely fail.
- **Release criteria.** Objectively measurable, documented, realistic conditions decided early that indicate the product is ready to ship (e.g., no open high-priority defects, performance goals met on all platforms, X% of tests passed). Tailor to the market segment (early adopters tolerate more defects than the late majority).
- **Risk.** A potential problem that could affect project success; "if you don't control risks, they will control you."
- **Achievable commitment.** A promise made only after good-faith negotiation, never one you know you cannot keep.

## Frameworks & models

**The four-step success-criteria process.**
1. **Define business objectives.** Answer "Why do we want to tackle this project?" Make objectives SMART (Specific, Measurable, Attainable, Relevant, Time-based) and prioritized. Examples span financial (capture X% market share by date; X% ROI within Y months; save $X/year by retiring a legacy system) and nonfinancial (customer-satisfaction targets, transaction throughput with accuracy, regulatory compliance, staff turnover below X%). Record them in the charter, vision-and-scope, or business case.
2. **Identify stakeholders and their interests.** Cover internal (project/program/product manager, sponsor, team, QA, infrastructure, marketing, sales, support, legal) and external (user classes, procuring customers, regulators, auditors, subcontractors, partners, the public). Perform a stakeholder analysis (benefits, attitudes, win conditions, constraints, and relative influence).
3. **Identify project constraints.** Budget, technology, environment, dependencies, regulatory limits.
4. **Derive project success criteria.** Translate objectives into trackable criteria, knowing you cannot make all of them the top priority - you must make thoughtful trade-off decisions or the team works at cross-purposes.

**The project charter contents.** Project description; business objectives and success criteria; stakeholders; vision; scope; assumptions and dependencies; constraints; milestones; business risks; resources. The charter is approved and then *used* throughout - at kick-off and whenever decisions arise.

**Risk management components.** Typical software risks: dependencies, requirements issues, management issues, lack of knowledge, outsourcing. The process: risk assessment (identify, estimate probability and impact), risk avoidance, risk control, documenting risks (condition–consequence form), and risk tracking. "Risk management can be your friend" by learning from the past.

**Wideband Delphi estimation.** A group estimation technique that yields more accurate estimates than any individual: Planning kickoff → individual preparation (each estimator lists tasks and estimates privately) → estimation meeting (anonymous estimates revealed, divergences discussed, re-estimated in rounds until convergence) → assemble tasks → review results. Surfaces overlooked tasks and reduces single-person bias.

**Contingency buffers ("Saving for a Rainy Day").** Build explicit buffers into schedules to absorb surprises, underestimates, and materialized risks. Sell the buffer to skeptics; size it deliberately; relates to Critical Chain Project Management (a single project buffer at the end rather than padding every task).

**Project retrospective process ("Looking Back, Looking Ahead").** Planning → kickoff → information gathering → issue prioritization → issue analysis → results reporting → action planning. Retrospectives are the principal source of the lessons-learned a manager should study during the *next* project's initiation.

## Patterns
- **Project charter** - intent: create shared stakeholder understanding of goals, scope, and success before work starts. When to use: at the launch of any non-trivial project. Trade-offs: must stay short and be actually used, not filed and forgotten.
- **Drivers / constraints / degrees of freedom** - intent: make explicit which of the five dimensions are fixed and which can flex, so trade-offs are negotiable. When to use: scoping and commitment negotiation. Trade-offs: requires honest classification; resist the temptation to call everything a constraint.
- **SMART business objectives** - intent: tie the project to measurable outcomes that imply success criteria. When to use: defining success. Trade-offs: measurability edges toward accountability, which can make participants uncomfortable.
- **Release criteria** - intent: pre-decide objective, measurable ship conditions to resist pressure to ship early. When to use: early in the project, tailored to the market segment. Trade-offs: must be realistic and agreed; otherwise ignored under pressure.
- **Wideband Delphi** - intent: produce better estimates and reveal overlooked tasks via structured, anonymous group estimation. When to use: novel or high-stakes estimates. Trade-offs: more time than a single estimate; needs a skilled moderator.
- **Inch-pebbles** - intent: decompose tasks to ~5–15 labor-hours (one to three days) to estimate accurately, reveal hidden work, and track fine-grained status. When to use: planning and progress tracking. Trade-offs: too fine for very large projects without grouping.
- **Contingency buffer / Critical Chain** - intent: absorb inevitable surprises with an explicit, defensible reserve. When to use: any schedule with real uncertainty. Trade-offs: must defend it against pressure to "just remove the padding."

## Principles & best practices
The book's 21 Project Management Good Practices, grouped:
- *Laying the foundation:* define project success criteria; identify drivers/constraints/degrees of freedom; define product release criteria; negotiate achievable commitments.
- *Planning the project:* write a plan (the hard part is the planning - thinking, negotiating, balancing - not the writing); decompose to inch-pebble granularity; develop planning worksheets for large recurring tasks; plan to do rework after every quality-control activity; manage project risks; plan for the learning curve; don't overschedule multitasking people; respect estimation and replan when reality changes.
- *Estimating the work, tracking progress, and learning for the future* round out the set.
- Use principled negotiation (Fisher/Ury/Patton): separate people from problem, focus on interests not positions, invent options for mutual gain, insist on objective criteria. Bring data from past projects; plan to renegotiate commitments when reality changes rather than pretending the original will hold.
- Avoid both over-planning and under-planning; the plan should be no longer than necessary to execute successfully, but always write one.

## Anti-patterns & pitfalls
- **Over-constrained project.** All five dimensions fixed (feature set, zero defects, fixed date, fixed team, fixed budget) - no way to absorb change; will most likely fail.
- **Schedule as the only success factor.** Delivering to spec on time and budget is worthless if it does not align with business objectives.
- **Taking a stakeholder's solution literally.** Skipping the "why" and building the requested feature that flops.
- **Pretend-commitments.** Letting everyone believe an unachievable commitment will hold "right up until the moment of disappointing truth."
- **Glossing over initiation.** Skipping chartering, stakeholder analysis, and risk identification "during the frenzy of launch."
- Metrics traps (ch. 13): lack of management commitment; measuring too much/too soon or too little/too late; measuring the wrong things; imprecise definitions; using data to evaluate individuals; measuring to motivate rather than understand; collecting unused data; lack of communication/training; misinterpreting data.

## Decision guidance
- Begin every project by working the four-step success process, then capture the results in a charter; use the charter as the reference point for downstream decisions.
- Classify the five dimensions explicitly; when a sponsor demands an aggressive schedule, negotiate: is schedule a constraint or a driver? What features (a degree of freedom) can be deferred? Can more staff or budget help?
- Decide release criteria early and tailor to the target market segment; hold to them under ship pressure.
- Identify and document risks at initiation; track them; use the lessons-learned repository from past retrospectives as input.
- For important or novel estimates, use Wideband Delphi; build explicit contingency buffers rather than hidden task padding.
- Negotiate achievable commitments using principled negotiation and project data; renegotiate openly when scope, staff, budget, or deadlines change.

## Source attribution
- 21 good practices, drivers/constraints/degrees of freedom, release criteria, inch-pebbles, plan-to-rework: Wiegers 2007, ch. 2.
- Four-step success process, SMART objectives, stakeholder analysis: ch. 4.
- Release criteria detail and Planguage: ch. 5; risk management: ch. 6; charter contents and use: ch. 7.
- Achievable commitments / principled negotiation: ch. 9; contingency buffers / Critical Chain: ch. 10; Wideband Delphi: ch. 11.
- Metrics primer and traps: ch. 12–13; learning from experience and retrospectives: ch. 14–15.

## Recent developments (2024–2026)

- **PMBOK Guide 8th Edition puts AI at the center.** The new edition (with the updated PMP Examination Content Outline launching July 1, 2026) treats AI and data analytics as a standard, cross-cutting member of the project team, intersecting all performance domains. It reintroduces explicit process guidance (around 40 processes including Develop Project Charter and Identify Risks, organized by Initiating/Planning/Executing/Monitoring & Controlling/Closing) presented non-prescriptively, and condenses the 7th Edition's 12 principles into 6. The initiation activities Wiegers emphasizes (charter, success criteria, risk identification) remain foundational and now sit inside an AI-aware standard. (PMI, pmi.org/standards/pmbok; PMBOK 8th Edition overviews, 2025-2026; BrainBOK, "PMBOK Guide 7th vs 8th")

- **AI is reshaping the initiation and planning toolkit.** Practitioner guidance (2025-2026) describes AI automating status updates and report generation, improving forecasting from historical data (predicting delays, overruns, resource conflicts), and strengthening risk management with predictive analytics, risk scoring, and early warnings. This extends Wiegers' risk-assessment and estimation chapters with data-driven anticipation, though the human work of negotiating achievable commitments and classifying drivers/constraints/degrees of freedom is unchanged. (OnlinePMCourses, "AI & Project Management: 2025 Review and 2026 Trends"; IIL, "The 7 AI Patterns Every Project Professional Should Know")

- **AI-assisted charter and worksheet drafting is now common.** Charter templates and project-initiation artifacts are increasingly AI-generated first drafts (e.g., Artifactly and similar tools), with the manager editing for accuracy and alignment, a faster path to the same shared-understanding artifact Wiegers advocates. The caution mirrors his metrics-trap warnings: a polished AI draft can imply false consensus that was never actually negotiated with stakeholders. (Artifactly project charter template, 2025)

- **Probabilistic forecasting strengthens estimation and buffers.** The broader move toward Monte Carlo / probabilistic delivery forecasting (see the Estimation and Planning topic) gives initiation-stage estimates and contingency buffers an empirical, confidence-interval basis, complementing Wideband Delphi for novel work and making the "saving for a rainy day" buffer easier to defend with data. (project-forecasting practice, 2024-2025)

- **Core practices stable.** The project charter, four-step success-criteria process, SMART business objectives, drivers/constraints/degrees of freedom, release criteria, Wideband Delphi, contingency buffers, and retrospectives remain sound and are reflected in the new PMBOK guidance. The 2024-2026 change is the embedding of AI as a forecasting, risk, and drafting aid across initiation, not a replacement for the negotiation and alignment work at the heart of the book.
