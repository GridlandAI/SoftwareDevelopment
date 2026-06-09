# Requirements Management and Change

**Domain:** 02 - Requirements & Specifications
**Primary sources:** Software Requirements Essentials (Wiegers & Hokanson, 2023); Software Requirements, 3rd Edition (Wiegers & Beatty, 2013); More About Software Requirements (Wiegers, 2009); Requirements Engineering: Foundation for Software Quality / REFSQ 2011 (Berry & Franch, eds., 2011)

## Overview
Once a team has good requirements, the work shifts from developing them to *managing* them. Requirements management is the RE subdomain that handles what happens to requirements over their life: prioritizing them, committing a subset to a development cycle (baselining), tracking versions and status, tracing them to related artifacts, and controlling the inevitable stream of changes. Cosmic Truth #3: *change happens.* Cosmic Truth #10: *you're never going to have perfect requirements* - and even if you did, they would be perfect only at one instant. The aim is not to prevent change but to absorb it with minimal disruption, keeping everyone aligned on what the team is - and is not - supposed to be building. This file covers prioritization, baselining, change control, traceability, requirements risk, and reuse.

## Core concepts

### What requirements management covers
- **Prioritization** - deciding what to build first to maximize value within time/budget/resource limits.
- **Baselining** - committing an agreed subset of requirements to a specific cycle/release.
- **Version control** - tracking evolving versions of individual requirements and sets.
- **Status tracking** - each requirement moves through a life cycle: proposed → approved (or rejected) → implemented → verified (or deferred/deleted/archived).
- **Traceability** - linking requirements backward to origins and forward to design, code, tests, and related requirements.
- **Change control** - receiving, assessing, deciding on, and folding in new/changed requirements.

Teams perform these selectively by risk; full traceability, for example, is mandatory for safety-critical/regulated products but often partial elsewhere. (Wiegers & Hokanson, 2023, Ch. 7)

### Prioritization
There are always more requirements than fit "the box" of time, budget, and resources. Prioritization weighs two dimensions: **importance** (how badly do we need it?) and **urgency** (how soon?). It is **relative, not absolute** - judging that one item delivers more value than another is enough; you rarely need a dollar figure. It begins at the *second* requirement and continues through and beyond release. Only compare items at similar granularity (use cases vs. use cases, not a use case vs. one functional requirement).

Factors influencing priority: customer/business value; contribution to iteration goals; who requested it; frequency of use; cost/time/difficulty; time-sensitivity (urgency, window, compliance, contract) and cost of delay; likelihood of change; dependencies; risk of implementing/delaying/omitting; whether it reduces future risk or enables strategic value; legal/regulatory/safety necessity. To test necessity, ask: what would you do without it? Is there a workaround? What is the cost/risk of its absence in release 1, or ever?

### Baselining
A **requirements baseline** is (1) an agreed-upon set of requirements approved for a specific development cycle, and (2) the software built from them. A baseline is a **snapshot in time** asserting that, to current knowledge, those requirements are correct, complete, and will achieve the desired outcomes. **Baselined does not mean frozen** - changes after baselining go through change control, which establishes a *new* baseline.

Two bounding strategies:
- **Time-bound** - fix a time box (iteration/release), then fill it with the highest-priority requirements up to development/testing capacity. Requires sizing. Common on agile projects (each iteration is a baseline).
- **Scope-bound** - group a logically related set of features/stories that build, test, and deploy together, then estimate size and schedule. If estimates exceed appetite, cut scope, add resources, or accept a later date (never cut quality).

Identify baseline membership via a subset SRS, attributes/tags in an RM tool, an iteration's story allocation, or by color-coding/annotating models (context diagram, DFD, ERD, feature tree with release numbers). Get explicit **agreement** from all relevant stakeholders; approval can be informal (agile story list) or formal (review + signed sign-off). Handle reluctant approvers by surfacing the real reason (often fear that approval freezes requirements - point them to the change process) or by tagging uncertain items as **stretch goals** outside the committed baseline. Teams often manage **multiple baselines concurrently** (one in test, one in development) and bundle iteration baselines into a release baseline; update the documented baseline to reflect what was actually built when it differs from the plan.

### Change control
Every project handles change somehow - the choice is whether it is ad hoc or systematic. An effective change control process is "a structure to manage change effectively, not a barrier." It begins when requirements are baselined and ends at delivery. It should answer: how is a change request submitted? what info is required, now and for impact assessment? against which baseline? who assesses size/impact? what criteria approve/reject? who approves and allocates to a baseline? how is the decision stored and communicated?

Keep it **as lightweight as possible.** A small co-located team may handle a change with a conversation between product owner and developer. Complex, multi-team, or distributed projects need a more formal process with a **Change (Configuration) Control Board (CCB)** - anywhere from one person to a cross-functional group (customers, development, testing, marketing, management, finance). Change requests follow a status life cycle: submitted → (evaluated) → approved/rejected → implemented → verified → closed (or canceled by the submitter at any point).

### Impact assessment
The BA must elicit, analyze, specify, and validate each change request just like an original requirement. Decision makers need cost/effort estimates (including requirements work on the change), priority/urgency, and a full understanding of impact on other components and tasks. Some changes are localized; others ripple across the ecosystem. A seemingly trivial request (change a service URL) can require invasive revisions to multiple systems - careful analysis prevents approving expensive, risky changes blindly. Use business objectives to judge whether the change even contributes to goals, and use models (traceability/requirements-mapping matrices, process flows, ecosystem maps, feature trees) to identify impacted areas quickly. Keep new/changed requirements logically or physically separate from the baseline until approved.

### Traceability
- **Backward tracing** - each requirement to its origin (stakeholder, higher-level requirement, business rule, regulation, use case).
- **Forward tracing** - each requirement to design elements, code, tests, and related requirements.

Easy partial tracing (tests to functional requirements/use cases/stories) is cheap; a **full requirements traceability matrix** takes tool support, process, discipline, and time, and is recommended for most projects but essential for certified/regulated/safety-critical products where certifiers must confirm each bit of functionality was designed, coded, and tested as intended. The **requirements mapping matrix (RMM)** links levels (business objectives → features → user requirements → business rules); a low-level item with no parent may be extraneous, a high-level item with no children may signal a gap.

### Requirements risk and reuse
- **Risk analysis** per requirement (Sommerville & Sawyer): performance impact, security threats, business-process impact, complexity/novelty, new techniques/technologies, risks to strategy/architecture/legal/environmental factors, adverse interactions with other systems. Safety-relevant products require **hazard analysis** in requirements analysis.
- **Reuse** - craft requirements to be reusable in other contexts and look for existing functionality to exploit. Generalize repeated patterns into templates (e.g., a vendor-integration template parameterized by location and key). Reusable assets: glossaries, business rules, process descriptions, stakeholder catalogs, data models, security/quality requirements. Requires an organizational store and a reuse-fostering culture.

## Frameworks & models

### Prioritization techniques
| Technique | How it works | Best for |
|---|---|---|
| **Three-level scale** | High (important + urgent), Medium (important not urgent), Low (nice-to-have) | Quick first cut |
| **MoSCoW** | Must / Should / Could / Won't (this time) | Coarse classification, stakeholder communication |
| **Pairwise comparison** | Compare each pair; float highest to top | Small sets (≤ ~15); especially quality attributes |
| **Rank ordering / stack ranking** | One linear sequence, one item on top | Small sets needing a strict order |
| **Kano model** | Basic (must-haves), Linear (satisfiers), Exciters/delighters, Indifferent | Distinguishing satisfaction drivers |
| **Relative weighting** | Rate benefit, penalty, cost, risk (1–9); spreadsheet computes priority | Analytical, feature-level |
| **WSJF (Weighted Shortest Job First)** | Cost of delay (value + time-criticality + risk reduction) ÷ size | Agile/SAFe backlogs |
| **Criteria matrix** | Weighted criteria (sum 100), rate each item, weighted score; subtract negatives | Multi-criteria, transparent |

Combine methods: a coarse MoSCoW/three-level pass, then rank-order or apply an analytical method within groups. An **MVP** (minimum viable product) is a prioritization-driven strategy - ship the smallest valuable slice to learn from the market. REFSQ research on **agile requirements prioritization** highlights gaps between literature and practice and the value of goal models as a precise criterion for feature pertinence. (Wiegers & Hokanson, 2023, Practice #13; Berry & Franch, 2011)

### Change control process model
A semiformal flow (Wiegers & Hokanson, Practice #20): request submitted (e.g., via team channel) → BA elicits details and assesses impact → CCB (e.g., dev team + PO + business partner) decides based on size, trade-offs against deferred work, and acceptability of those trade-offs → if approved, allocate to a baseline, version the requirement, update related models, define new baseline → communicate decision to affected stakeholders → implement → verify → close. Reject decisions are documented with rationale to speed a future re-request.

### Anticipating and absorbing change
- **Identify volatile areas early** (rule-dominated or growth-prone) and design them as **configuration/data-driven** rather than hardcoded (e.g., currency-by-configuration so adding 20 countries needs only data/config changes, not code).
- **Build in contingency buffers**: feeding buffers at the end of dependent task chains/iterations, a project buffer at the end of a release/project, reserved iteration capacity, and a contingency-buffer iteration. Without buffers, the first change (or low estimate, or departure, or materialized risk) breaks commitments. Change always has a cost - even discussing and rejecting a change has a price.

## Patterns

- **Two-dimensional prioritization (importance × urgency)** - *intent:* sequence work for maximum timely value. *When:* always; revisit each cycle. *Trade-offs:* relative judgments only; combine coarse + analytical methods for large sets.
- **Time-bound vs. scope-bound baseline** - *intent:* commit a deliverable, manageable set. *When:* time-bound for fixed-cadence agile; scope-bound for feature-complete releases. *Trade-offs:* time-bound needs sizing up front; scope-bound risks schedule surprises.
- **Stretch goals outside the baseline** - *intent:* let teams commit confidently while pre-approving extras. *When:* uncertainty about capacity. *Trade-offs:* requires clear "committed vs. stretch" tracking.
- **Lightweight change control with a CCB** - *intent:* informed, timely change decisions without a barrier. *When:* any baselined project; scale formality to project complexity. *Trade-offs:* too heavy → people bypass it; too light → chaos and unapproved changes.
- **Impact assessment before decision** - *intent:* expose ripple effects and true cost. *When:* every change request. *Trade-offs:* assessment time; prevents costly blind approvals.
- **Configuration/data-driven design for volatile rules** - *intent:* absorb frequent change without code edits. *When:* rule-heavy or fast-evolving areas (fares, fees, currencies). *Trade-offs:* upfront design effort; dramatically cheaper evolution.
- **Contingency buffers** - *intent:* protect commitments against inevitable change/risk. *When:* every plan. *Trade-offs:* apparent slack; the alternative is broken commitments.
- **Reuse templates** - *intent:* avoid re-specifying repeated patterns. *When:* recurring integrations/processes. *Trade-offs:* needs a store and reuse culture.

## Principles & best practices
- Prioritize relatively and continuously; only compare like-granularity items.
- Baseline does not mean frozen - it initiates change control, it does not end change.
- Get explicit stakeholder agreement on each baseline; record approvals; update the baseline to match what was actually built.
- Define and *follow* a change control process early, even a simple one; keep it lightweight enough that people use it instead of bypassing it.
- Always assess impact (cost, effort, ripple, risk, regulatory) before deciding; use business objectives as the relevance test.
- After every decision: document it (with rejection rationale), update the baseline if approved, version requirements and models, and communicate to all affected parties.
- Trace pragmatically - full traceability where certification/regulation demands it, targeted tracing (tests ↔ requirements) elsewhere.
- Anticipate change: identify volatile areas, design them configuration-driven, and reserve contingency buffers.
- Do not use "we can adapt to change" as an excuse to skimp on understanding needs - good RE reduces the *number* of changes.

## Anti-patterns & pitfalls
- **Expecting the loudest voice to set priority** (Wiegers' pitfall list) - favored user classes aligned with business objectives should carry more weight.
- **Failing to prioritize at all** - everything becomes "required," nothing gets sequenced.
- **Treating baselined as frozen** - breeds resistance to legitimate change and disappointing releases.
- **No change control process** - stakeholders revise features without telling the team; chaos and unimplemented requests.
- **Burdensome change control** - people route around it; changes slip in the back door.
- **Approving changes without impact analysis** - a "simple" change turns out to touch three systems; risk of breaking regulations/business rules.
- **No contingency buffers** - the first change/risk/departure breaks delivery commitments.
- **Hardcoding volatile rules** - every rule change becomes a code change.
- **Using adaptability as an excuse to skip thinking** - change is real, but most changes stem from fuzzy objectives, overlooked stakeholders, and poorly understood requirements, all preventable.

## Decision guidance
- **Which prioritization method?** Use the simplest that works. Coarse and communicative → MoSCoW or three-level. Quality attributes or small sets → pairwise comparison. Transparent multi-criteria → criteria matrix or relative weighting. Agile flow economics → WSJF. Satisfaction analysis → Kano. Combine coarse + analytical for large backlogs.
- **Time-bound or scope-bound baseline?** Fixed cadence / continuous delivery → time-bound. Feature-complete, "ship when ready" releases → scope-bound. Project type and life cycle largely decide.
- **How formal should change control be?** Scale to impact and team distribution. Small co-located team / small change → conversation between PO and developer. Multi-team, distributed, regulated, or broad-impact change → formal CCB with documented assessment and sign-off. Start simple; add layers only when needed.
- **How much traceability?** Full bidirectional matrix for safety-critical/regulated/certified products. Targeted tracing (tests to requirements; requirements to sources) for ordinary business systems and apps - recommended but not exhaustive.
- **Hardcode or configure?** If an area is rule-dominated or expected to grow/change frequently, make it configuration/data-driven; otherwise hardcoding is fine.

## Source attribution
- Prioritization (factors, the technique table, pairwise/criteria-matrix/relative-weighting/WSJF/Kano/MoSCoW/MVP), baselining (time-bound vs. scope-bound, baseline identification, stretch goals, multiple/concurrent baselines, getting agreement), change control (process, CCB, status life cycle, impact assessment, post-decision actions), anticipating change (volatile areas, configuration-driven design, contingency buffers), and the requirements-management activity list are from **Software Requirements Essentials** (Wiegers & Hokanson, 2023, Ch. 7, Practices #13, #19, #20) and **Software Requirements, 3rd Edition** (Wiegers & Beatty, 2013).
- The Cosmic Truths on change and imperfect requirements, the scope-creep guidance, and requirements-quality data collection are from **More About Software Requirements** (Wiegers, 2009).
- Agile requirements prioritization (practice vs. literature) and goal-model-based prioritization are from **Requirements Engineering: Foundation for Software Quality / REFSQ 2011** (Berry & Franch, eds., 2011).
- Per-requirement risk analysis dimensions trace to Sommerville & Sawyer (1997), as cited in Wiegers & Hokanson (2023).

## Recent developments (2024–2026)
Requirements management fundamentals (prioritization, baselining, change control, traceability, impact assessment) are stable. The notable 2024-2026 developments are AI assistance for traceability and impact analysis, and faster-arriving change driven by AI-accelerated delivery.

- **AI-assisted traceability and impact analysis in tooling.** Modern RM platforms now generate and maintain trace links, flag downstream impacts when an upstream item changes, and offer one-click impact analysis (Jama Connect's live traceability, Visure's AI-driven analysis). Some regulated teams report cutting trace-matrix maintenance time substantially with generative AI, easing the discipline cost that has always limited full traceability. (Jama Software, 2025-2026, jamasoftware.com/blog/ai-requirements-management; Visure, 2025)
- **AI-generated change-request drafts and assessments, with human decision-making preserved.** AI is used to draft change requests, summarize affected areas, and estimate ripple effects, supporting the BA's impact-assessment work; but the change-control decision (and the CCB) remains a human/governance function, consistent with this file's "assess impact before deciding" principle. (Jama Software, 2025)
- **Change arrives faster under AI-accelerated delivery.** Because AI shortens build time, stakeholders request changes more readily and changesets grow larger (DORA 2025 notes larger AI changesets raise change-failure risk), increasing the importance of lightweight-but-real change control, contingency buffers, and configuration-driven design for volatile areas. The fundamentals get more load, not less. (DORA / Google Cloud, 2025)
- **Enterprise RM platforms consolidate around AI features for regulated work.** Jama Connect, IBM DOORS Next, Siemens Polarion, PTC Codebeamer, and Perforce ALM lead traceability-heavy, regulated development and have added AI quality/trace assistants, while Jira-based stacks still struggle with versioning, baselining, and impact analysis for regulated industries, reaffirming the file's "scale formality to risk" guidance. (The Digital Project Manager, 2026; Jama Software, 2025)
- **Regulation raises traceability stakes for AI systems.** The EU AI Act's documentation, data-quality, and human-oversight requirements for high-risk AI systems (phasing in through August 2026) extend the "full traceability for regulated products" rule into AI-intensive systems, including tracing training-data and model decisions, not just code. (European Commission, 2024-2025)
