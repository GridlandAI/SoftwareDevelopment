# Enterprise Architecture in Practice

**Domain:** 12 - Enterprise and Business Architecture
**Primary sources:** Doing Enterprise Architecture: A Maturity-Model Guide to Architecture Development (Tom Graves & Slade Beard, 2025); The Survivor's Guide to Enterprise Architecture (Chloe Gray, Martin Sykes & Paul Homan, 2025); Fundamentals of Enterprise Architecture (Tanusree McCabe, 2024)

## Overview

Knowing what enterprise architecture *is* (frameworks, domains, operating models) is necessary but not sufficient. The harder problem is *doing* it: building capability step by step, surviving organizational politics, earning a mandate, delivering value fast enough to matter, and staying out of the ivory tower. This file captures the practitioner craft - maturity models, governance, stakeholder engagement, portfolio and decision management, and the survival skills of the EA role.

Two complementary lenses run through the sources. Graves & Beard offer a *developmental* lens: a maturity model that builds architecture capability through Foundations plus five steps, each respecting dependencies on the ones below. Gray, Sykes & Homan offer a *survival* lens organized as Beginning -> Doing -> Managing -> Being: how to start in the first 90 days, do "just-enough-architecture, just-in-time," manage the portfolio and governance, and *be* a trusted advisor. McCabe adds the strategic-objectives and assessment perspective. The unifying message: EA delivers value only when the value is *realized* - "our job as enterprise architect is not over until the value is realized" (Gray/Sykes/Homan); and "you cannot put a mature architecture practice into a less mature organisation."

## Core concepts

**Effectiveness as the goal (Graves & Beard).** Effectiveness is treated as a quality-concern like safety and security: it is about effectiveness of *outcomes* (for the organization and its broader shared-enterprise) and of *change itself* (every change should leave things better able to change again). The aim is an enterprise that responds well to anything the real world throws at it - opportunities, risks, disruption - in ways that are effective not just in outcomes but within itself. The end goal is *everyone taking responsibility for architecture* under the architect's guidance.

**Mandate and sponsorship (Gray/Sykes/Homan).** EA cannot function without executive sponsorship. Practitioners must identify *why now / what changed*, *who is my sponsor*, *who has the power*, and *what motivates a sponsor*, then define scope accordingly. Sponsors and power shift over time, so finding and changing sponsors is an ongoing activity.

**The EA operating model (Gray/Sykes/Homan).** An architect balances three categories of activity, "spinning three plates":
1. **Development** - analyzing requirements; creating models, standards, patterns, and guidance for the desired direction.
2. **Operations** - using the architecture to govern and manage change.
3. **Organisation** - building/maintaining the team, defining roles and responsibilities, developing relationships, balancing the model.

Balancing these is crucial, especially in the first 90 days. Before creating anything new, find out what already exists and how relevant it is - many architects burn early effort on low-value maps that don't match sponsor expectations.

**Just-enough-architecture, just-in-time (Gray/Sykes/Homan).** Architecture is created to order, driven by near-term demand, not "for stock." "Just enough" applies to both depth and breadth; most materials will not be fully finished but must be good enough to support decisions with a reasonable tolerance for error - and enough to substantiate the recommendation. The faster you work, the more you must collaborate, facilitate, and negotiate with stakeholders and SMEs. Use a Kanban flow with MoSCoW (Must/Should/Could/Will-not-do) prioritization. Not everything is just-in-time - most organizations still have long-term strategic plans, target architectures, and standards.

## Frameworks & models

### The maturity-model approach (Graves & Beard)

Capability is built in increments structured as a maturity model: a **Foundations** section plus five steps. Each step adds a capability-increment that depends on the ones below it. The steps are cross-mapped to CMMI v1 levels (ad-hoc, repeatable, defined, managed, optimized), with two crucial caveats:
- CMMI levels apply *within* each step too - capabilities in a step become fully effective only at that step's "optimized" level.
- The maximum effectiveness at any step is capped by the *lowest* CMMI level achieved in the steps below it. If Step 1 is still ad-hoc, nothing above it rises much above ad-hoc. This is why EA initiatives that start halfway up (digital/business transformation) without foundations or Step 1 tend to fail.

The steps map to layered perspectives on the organization-enterprise relationship (illustrated with an airport):
- **Foundations** - core change-work capabilities and discipline (sense-making, the art/science/patterns/layers/cycles/practice of change).
- **Step 1: Identify the Enterprise (Boundary)** - establish effectiveness- and success-criteria for the organization and its enterprise context; enough to guide ad-hoc changes that have a good chance of working.
- **Step 2: Get Ready for Action (Inside-in)** - develop and maintain internal infrastructure and services that support business models reliably and repeatably.
- **Step 3: Set Out the Stall (Inside-out)** - define and deliver the business-services, products, and models presented to the outside world, adapting to the market.
- **Step 4: Interact with the Market (Outside-in)** - manage everyday real-world complexity (customer service, recruitment, regulation, breakdowns) on-track to enterprise values.
- **Step 5: Interact with the Enterprise (Outside-out)** - improvise and optimize around major disruption and unplanned change (including reputation management at industry scale).

Work can be done out of sequence to meet urgent needs, but doing so respects the dependencies poorly and is rarely effective long-term ("beginner's luck" that can't be repeated).

### Assessment: capability model over rigid maturity model (McCabe)

McCabe critiques classic five-level maturity models as static, context-free, linear, and burdened by negative connotations for "immature" areas. She proposes an **outcome-based capability assessment framework** instead: (1) **Define business outcomes** quantitatively, with stakeholders; (2) **Identify capabilities** needed across people (culture), process, and tools, treating EA itself as a domain; (3) **Map capability gaps** via research and stakeholder interviews. This dynamic aid plus a prioritization schema focuses improvement where it brings most benefit, rather than chasing a fixed end-state.

Gray/Sykes/Homan add a pragmatic note: an inherited team may warrant a process-maturity measure (US DoC Architecture Capability Maturity Model; CMMI from Carnegie Mellon SEI), but you should also assess the maturity of the *organization's strategy process* - mismatched maturity between inputs and EA causes friction. Moving up a level costs effort that could go to architecture work, so choose an *appropriate* maturity level for the need.

### Baseline measures (Gray/Sykes/Homan)

Capture a baseline early (value diminishes if taken late). Candidate measures: architecture coverage (use cautiously - 100% coverage is an anti-goal), project alignment (and exceptions per project), architectural debt, time-to-change, cost savings/avoidance, stakeholder satisfaction, governance effectiveness (% of decisions implemented next quarter), and reuse rate. **Measure impact, not volume** - there is no value in counting documents or models, and a metric like "reduce exceptions" is trivially gamed by simply not looking for exceptions.

### Governance structures and styles (Gray/Sykes/Homan)

Governance must support option creation/analysis, define clearly how decisions are made, facilitate communication and retrieval of decisions, and provide a route for exemptions. Six styles (mix and match, keep it as simple as possible): **participatory**, **directive**, **resource-driven** (budget-holders dominate), **committee-based**, **expert-led**, and **flexible** (high autonomy, teams self-organize then integrate). Decision-making can be centralized or **federal** (central standards + regional autonomy). Hierarchies of boards work like a judicial system (local design review -> design authority -> architecture governance board -> executives/CEO) but must avoid bureaucracy and infinite appeal. The most common board is the **design authority** at the operational level. A balanced model pairs an **architecture governance board** (enforcement) with an **architecture advisory group** (support/steer) so stakeholders seek help early. Anchor governance to the IT portfolio/investment triage to catch demand early. Avoid becoming an *audit/policing* function - "architecture as a policing activity does not work."

### The three sources of risk (Gray/Sykes/Homan)

Watch for risk that is **top-down** (business needs/direction change), **bottom-up** (something broken; obsolescence, technical debt, ripple effects), and **outside-in** (a supplier changes their system/business model; a new technology disrupts; a new regulation changes legality). Build a risk model to gauge degree of risk and act proportionately; partner with information security in designing the governance and risk process.

### Six survival skills - "Being" an enterprise architect (Gray/Sykes/Homan)

There is no consistent path to the role; successful architects share six skills:
- **Trusted advisor** - most architects advise rather than decide. Trustworthiness (Maister/Green/Galford trust equation) = (credibility + reliability + intimacy) / self-orientation. Build credibility (knowledge, track record, translating across domains), reliability (consistent, repeatable delivery; break work into regular small deliverables), intimacy (emotional connection, active listening, passing tough messages), and low self-orientation (organization's needs over your own; beware chasing the next "new thing"). Set *reasonable expectations* up front rather than "managing expectations" after.
- **Critical thinker** - gather relevant information, understand relationships, avoid bias, make reasoned judgments. Guard against survivor bias, sunk-cost bias, herd bias, and consistency bias (blind reuse). Shift information between perspectives to test viability (for example, vendor "modules" vs. logical groupings).
- **Communicator** - storytelling and visualization as core tools; one message will not work for everyone.
- **Influencer** - persuasion is hard; influence comes from trust, not positional authority.
- **Negotiator** - start with a plan; know when to stop.
- **Change agent** - conceive ideas *and* bring them to fruition ("the doers are the major thinkers").

## Patterns

- **Maturity-model capability development (Foundations + 5 steps)** - Intent: build EA capability in dependency-respecting increments, each cross-mapped to CMMI levels, so foundations are solid before transformation. When to use: standing up or maturing an EA practice. Trade-offs: reliable, repeatable effectiveness vs. slower than jumping to high-value transformation; effectiveness is capped by the weakest lower step.
- **Outcome-based capability assessment** - Intent: assess EA by defining business outcomes, identifying needed people/process/tools capabilities, and mapping gaps, instead of scoring against a static maturity ladder. When to use: deciding where to invest in the EA function. Trade-offs: dynamic and context-fit vs. less comparable/benchmarkable than a fixed model.
- **Just-enough-architecture, just-in-time** - Intent: produce architecture to order against near-term demand using Kanban + MoSCoW, accepting "good enough" depth and breadth. When to use: fast-moving organizations where completeness is impossible. Trade-offs: speed and relevance vs. incomplete artifacts and heavy reliance on facilitation/negotiation; not suited to genuinely long-horizon target-setting.
- **Balanced governance: governance board + advisory group** - Intent: pair an enforcing architecture governance board with a supporting advisory group so stakeholders seek early help and approval later. When to use: any organization formalizing architecture oversight. Trade-offs: support-plus-enforcement balance vs. the cost of running two bodies; must avoid becoming a bottleneck or audit function.
- **Architecturally significant decision triage** - Intent: define criteria for which decisions warrant the governance process and provide a quick triage so trivial decisions are delegated. When to use: protecting governance capacity and delivery speed. Trade-offs: faster flow and clear thresholds vs. risk of mis-triaging significant decisions.
- **Federal governance (central standards + regional autonomy)** - Intent: a central group sets certain standards and grants defined autonomy to business-unit/regional architectures. When to use: large, multi-region or multi-BU organizations. Trade-offs: local fit and scale vs. potential inconsistency and integration cost.
- **Trust-equation advisory model** - Intent: operate as a trusted advisor by maximizing credibility, reliability, and intimacy while minimizing self-orientation. When to use: when the architect advises rather than decides (the common case). Trade-offs: durable influence without authority vs. slow to build and easily destroyed by a single lapse.
- **First-90-days mandate playbook** - Intent: secure executive sponsorship, find early-use customers/scenarios, deliver quick wins, stand up lightweight governance, time-box deliverables (80/20), and communicate per-audience. When to use: starting an EA role or practice. Trade-offs: builds reputation and momentum vs. pressure to show value before foundations are deep.

## Principles & best practices

- **Value is not realized until the change is delivered.** Architects must be both thinker and doer; the job isn't done at the document stage (Gray/Sykes/Homan).
- **Match your practice to the organization's maturity.** Do not import a mature practice into a less mature organization; raise maturity only where the need justifies the effort (Gray/Sykes/Homan).
- **Respect step dependencies.** Build foundations and Step 1 before transformation; effectiveness is capped by the weakest lower capability (Graves & Beard).
- **Solve problems your sponsor and stakeholders care about.** Focus EA implementation on realized value, not artifact correctness in isolation (Gray/Sykes/Homan; McCabe's "effective" theme).
- **Keep governance as simple as possible and oriented to support, not policing.** Catch demand early via portfolio triage; pair enforcement with advice (Gray/Sykes/Homan).
- **Set reasonable expectations rather than managing them after.** Clarity up front, with confirmation that the message was understood (Gray/Sykes/Homan).
- **Involve participants from all parts of the organization.** EA definition and management must not be restricted to enterprise architects (Gray/Sykes/Homan).
- **Time-box with an iterative approach and a streamlined approval process.** Apply the 80/20 rule (Gray/Sykes/Homan).

## Anti-patterns & pitfalls

The five negative perceptions to avoid (Gray/Sykes/Homan):
- **The ivory tower** - overly academic architecture disconnected from practical reality. ("The ivory tower is real. It's where the people who don't make a difference find themselves.")
- **Boiling the ocean** - trying to solve every aspect of the EA at once; broad, slow, low-impact.
- **Analysis paralysis** - each step of analysis demanding more analysis, leading to anxiety-inducing conclusions and no decision.
- **Heavy governance** - every decision needs approval, creating a bottleneck; often spirals after one bad project decision triggers ever-deeper reviews.
- **The "NO department"** - the worst bottleneck, where nothing not originated by the architect is ever approved.

Other pitfalls:
- **Starting in the middle / skipping foundations** - transformation programs without Step 1 or foundations fail (Graves & Beard).
- **Measuring volume instead of impact** - counting models/documents, or gameable targets like "reduce exceptions" by not looking (Gray/Sykes/Homan; McCabe).
- **Becoming an audit/policing function** - destroys collaboration and the value of carefully crafted guidance (Gray/Sykes/Homan).
- **Excluding groups from governance** - top-management-only boards lose the insight of those who run day-to-day operations and reduce the ability to influence them.
- **Cognitive biases** - survivor, sunk-cost, herd, and consistency bias degrade architectural decisions (Gray/Sykes/Homan).
- **Chasing the next "new thing"** - high self-orientation toward novel technology beyond the organization's actual need erodes trust (Gray/Sykes/Homan).

## Decision guidance

- **Where to spend the first 90 days:** understand the sponsor's needs and the existing content first; secure executive sponsorship; pick early-use scenarios for quick wins; stand up lightweight governance; balance Development/Operations/Organisation; baseline a few impact measures. Don't start by mapping the whole estate.
- **How fast/complete to make architecture:** default to just-enough, just-in-time for near-term demand (Kanban + MoSCoW), but keep a longer-horizon track for strategic plans, target architectures, and standards. Choose depth/breadth by what the current decision actually requires.
- **How heavy to make governance:** size it to the material and its use; keep boards minimal with clear scope and escalation routes; triage for architectural significance and delegate the rest; pair an enforcing board with an advisory group; avoid policing. In large/multi-region orgs, use federal governance.
- **How mature to make the practice:** assess organizational and strategy-process maturity alongside EA maturity; raise EA maturity only where the gap causes friction and the benefit justifies the effort; never impose a higher-maturity process than the organization can absorb.
- **How to operate without authority:** lead through the trust equation - build credibility/reliability/intimacy, keep self-orientation low - and through communication, influence, negotiation, and change-agency rather than mandate.
- **How to prove value:** tie work to outcomes the sponsor cares about, deliver in regular small increments, and baseline impact early so progress is demonstrable.

## Source attribution

- *Effectiveness as a quality-concern, the maturity-model with Foundations + five steps, CMMI cross-mapping and the dependency cap, boundary/inside-in/inside-out/outside-in/outside-out perspectives, foundations of change-work, engaging others in the architecture:* Doing Enterprise Architecture: A Maturity-Model Guide - Tom Graves & Slade Beard (Apress, 2025), Chapters 1-14.
- *Mandate and sponsorship, the EA operating model (development/operations/organisation), just-enough-architecture just-in-time, baseline measures and "measure impact not volume," appropriate maturity, the five negative perceptions/anti-patterns, governance structures/styles/hierarchies and the board+advisory model, the three sources of risk, the six survival skills (trusted advisor with the trust equation, critical thinker and biases, communicator, influencer, negotiator, change agent), first-90-days key messages:* The Survivor's Guide to Enterprise Architecture - Gray, Sykes & Homan (BCS, 2025), Chapters 1-2, 4-9, 15, 17-22.
- *Outcome-based capability assessment framework vs. classic maturity models, OKR/KPI-based strategy objectives, delivering measurable business value:* Fundamentals of Enterprise Architecture - Tanusree McCabe (O'Reilly, 2024), Chapters 2, 12.

## Recent developments (2024-2026)

- **The EA practitioner's tooling shifts toward AI assistance.** Through 2025 the practical work captured here (maintaining repositories, capability mapping, artifact creation, data validation, drafting recommendations) is increasingly augmented by agentic AI in EA suites that proactively surfaces patterns, anti-patterns, and architectural alternatives and lets stakeholders query EA knowledge conversationally, which directly supports the "just-enough, just-in-time" and "measure impact not volume" disciplines in the source material. (ACL Digital, 2025: https://www.acldigital.com/blogs/how-ai-augmented-enterprise-architecture-is-redefining-business-agility-in-2025-and-beyond ; Go-TOGAF: https://www.go-togaf.com/how-ai-is-revolutionizing-enterprise-architecture-trends-tools-and-tactics/)
- **Scenario simulation as a 2025 EA practice.** A noted trend is using AI to simulate and stress-test multiple future states rather than only documenting the as-is, giving architects a faster way to produce the target architectures and option analysis that governance bodies need. (Intelance, 2025: https://www.intelance.co.uk/enterprise-architecture-2025/ ; Staun & Stender: https://www.staunstender.com/article/the-future-of-enterprise-architecture-in-the-age-of-ai/)
- **TOGAF Series Guide on Architecture Roles and Skills (2024).** The Open Group added a roles-and-skills guide to the 10th Edition series, useful for the "being an enterprise architect," sponsorship, and capability-development themes here. (The Open Group, 2024-2025: https://blog.opengroup.org/2025/07/01/navigating-the-togaf-standard-10th-edition-updates-and-insights/)
- **The trusted-advisor and governance craft remains central, with new AI-governance demands.** As AI agents enter enterprise applications (Gartner: 40% of enterprise apps with task-specific agents by end of 2026, up from under 5% in 2025), the architect's mandate, sponsorship, risk, and governance responsibilities in the source material now explicitly extend to AI literacy, trust, and ethical governance, raising the value of the survival skills (trusted advisor, critical thinker, change agent) rather than diminishing them. (Gartner, Aug 2025: https://www.gartner.com/en/newsroom/press-releases/2025-08-26-gartner-predicts-40-percent-of-enterprise-apps-will-feature-task-specific-ai-agents-by-2026-up-from-less-than-5-percent-in-2025)
- **Maturity and outcome-based assessment guidance unchanged.** The maturity-model dependency cap, outcome-based capability assessment, and impact-over-volume measurement remain current; the 2024-2026 development is AI-assisted execution of EA work, not a new practice model.
