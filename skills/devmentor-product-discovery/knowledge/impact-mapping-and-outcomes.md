# Impact Mapping and Outcomes

**Domain:** 03 - Product Discovery and Planning
**Primary sources:** Impact Mapping (Gojko Adzic, 2012)

## Overview
Impact mapping is a lightweight, collaborative planning technique that connects what a delivery team builds (deliverables) to the business goal it is supposed to achieve, by way of the actors whose behavior must change and the impacts (behavior changes) that move the organization toward the goal. It is a mind map grown during a facilitated workshop with senior business and technical people, answering four questions in order: Why? Who? How? What? The point is to keep teams from delivering long shopping lists of features that miss the business objective, and instead to deliver business goals while visualizing and testing the assumptions behind each feature.

In the SDLC, impact mapping bridges strategy and delivery. It produces a goal-oriented roadmap that ties scope to outcomes, supports adaptive and iterative delivery, complements Lean Startup's build-measure-learn by helping choose which assumptions to test, and lets teams report progress in terms of impacts achieved rather than tasks completed. Its core stance: a milestone that delivers a different scope but hits the business goal is a success; one that delivers the requested scope but misses the goal is a failure.

## Core concepts

**The four levels of an impact map (Why → Who → How → What):**
- **Goal (Why?).** The center of the map. Why are we doing this? Good goals are SMART (Specific, Measurable, Action-oriented, Realistic, Timely), present the problem to be solved rather than a solution, avoid design constraints, and ideally link clearly to money. Chris Matts's suggestion: define a model of business value first, then express goals as increments of that value. Examples: "Increase user conversion by 20% in three months"; "Start trading in Brazil by March next year."
- **Actors (Who?).** Whose behavior do we want to impact? Who can produce or obstruct the desired effect? Be specific; avoid generic "users." Alistair Cockburn's three actor types: primary actors (whose goals are fulfilled, e.g., players), secondary actors (who provide services, e.g., a fraud-prevention team), and off-stage actors (interested but not directly benefiting or serving, e.g., regulators, senior decision-makers). Define actors in order of specificity: individual → persona → role/job title → group/department. Examples: "Mike Smith from marketing," "under-18 users with a mobile device at a concert," "Apple iTunes store approvers."
- **Impacts (How?).** How should actors' behavior change to help (or how could they obstruct) reaching the goal? Impacts are behavior changes, not product features. List only impacts that genuinely move toward the goal. Show a change versus current behavior (not "selling tickets" but "selling tickets five times faster"). Consider hindering impacts as well as positive ones. Once you find an actor's first impact, ask what else they could do. Examples: "inviting more friends," "purchasing tickets without calling the call centre," "selling tickets faster."
- **Deliverables (What?).** What can we do, as an organization or delivery team, to support the required impacts? Software features and organizational activities. This is the least important level; do not make it complete up front; refine iteratively. Treat deliverables as options, not commitments. Keep them high level early; break down later into user stories or use cases as deeper map branches. Consider non-software ways to create an impact (e.g., advertising to recruit players rather than rebuilding a system). Examples: "online ticket sales," "mobile home page with purchase form," "optimise call-centre sales scripts."

**Deliverables as assumptions.** A map encodes two assumption levels: that a deliverable will produce the desired impact, and that the impacts together achieve the goal. Visualizing this chain of reasoning lets teams scrutinize and re-evaluate decisions as new information arrives during delivery.

**Never aim to implement the whole map.** Instead, find the shortest path through the map to the goal. The hierarchy lets you group related deliverables, compare them, avoid over-investing in less important actors/impacts, and throw out deliverables that do not contribute to any important impact.

## Frameworks & models

**Creating an impact map - three preparation steps, then four mapping steps.**

Preparation (small group, no more than five or six people, with key business and technical decision-makers):
1. **Discover real goals.** Get a clear mission expressed as expected business goals. List candidate goals on a whiteboard, argue and compare them, and agree on the number of objectives per milestone (ideally one objective per milestone). Counter the human tendency to jump straight to features-as-solutions.
2. **Define good measurements.** Establish metrics that tell you when the goal is met, so the plan can be re-evaluated as information arrives. Beware metrics becoming an end in themselves (James C. Scott, "Seeing Like a State").
3. **Plan your first milestone.** Define a milestone scoped to a clear, measurable objective.

Mapping (larger group, 10-20, with a dedicated facilitator; split into sub-groups to diverge and merge):
1. **Draw the map skeleton.** Put the first milestone in the center and connect some key high-level deliverables. If a shopping list of requirements exists, reverse-engineer a few crucial items (not the whole list) onto the skeleton; this validates the team's prior thinking and has a positive psychological effect. Double-check: Is it realistic the feature contributes to the impact? Is the impact valid for the actor? Will the impact really help achieve the goal? Form sentences like "Someone can help us achieve our objective by doing something differently."
2. **Find alternatives (divergent phase).** Time-boxed, limited to actors and behavior impacts. Diverge and merge: groups work independently, review every 20 minutes. Ask: What else could those actors do for us? Who else can help, and how? Who can obstruct us? Do not criticize ideas. Use grid-passing or collaborative games (Innovation Games, Gamestorming) to push ideation.
3. **Identify key priorities (convergent phase).** Keep it informal. Ask: What crucial obstructions could stop us before we start? Is there a high-value low-hanging-fruit impact? What are the key assumptions to test? Use dot-voting (red for critical, green for low-hanging fruit) or virtual cash if no natural answer emerges. Prioritize impacts, not deliverables. For more structure, use the Kano model (must-have / linear / exciter) or the purpose-alignment model (market-differentiating / parity / partner / who-cares).
4. **Earn or learn.** Grow the deliverables level so each item is either justified by a validated assumption or fits a defined "learning budget" (the max cost/length of learning tasks, often one day to one week). Ask: What is the simplest way to support this activity? If unsure about an assumption, what is the simplest way to test it? Could we test it without software? Could we start earning with a partly manual process? Use user-story mapping or the "hamburger" method to find iterative slices.

**Connections to other methods.**
- *Adaptive planning / the Palchinsky method (Tim Harford, "Adapt"):* variation (seek new ideas - find alternatives), survivability (try things where failure is survivable - small deliverable chunks), selection (seek feedback and learn - good measurements). Big upfront plans rarely survive contact with reality, so maps provide a basis for feedback on assumptions.
- *Lean Startup build-measure-learn:* impact mapping complements it by visualizing assumptions so you can choose the most valuable experiments aligned with company goals; the metrics on an impact define what to measure.
- *Iterative/incremental delivery:* maps give business and delivery a shared big picture, avoid major upfront analysis, prevent "Water-Scrum-Fall," and let teams commit to impacts they plan to support rather than features they plan to implement.
- *Roadmap management:* the hierarchical visualization makes maps a strong roadmap tool; report progress as impacts supported or planned, not features shipped.
- *Switch from cost to investment:* because goals translate to money, discuss return on investment and incremental investment (spend a small percentage to prove key assumptions; stop if they fail; keep investing until the goal is reached) instead of adversarial cost/time estimation.

## Patterns

**Goal-Actor-Impact-Deliverable map** - Intent: connect every deliverable to a business goal through actor behavior changes, exposing assumptions. When to use: project/milestone kickoff, roadmap planning, scoping, prioritization. Trade-offs: needs senior decision-makers and technical experts in the room and skilled facilitation; the map is a living artifact, not a one-off.

**Shortest path through the map** - Intent: deliver the goal with minimal scope by choosing the cheapest set of impacts/deliverables. When to use: scoping a milestone. Trade-offs: requires discipline to leave most of the map unbuilt.

**Earn-or-learn deliverable scoping** - Intent: justify each deliverable by a validated assumption or fit it within an explicit learning budget. When to use: growing the deliverables level. Trade-offs: requires honesty about what is known vs. assumed; needs an agreed learning budget.

**Diverge-and-merge facilitation** - Intent: get the wisdom of crowds without one strong voice dominating, and benefit from set-based design. When to use: the find-alternatives mapping step. Trade-offs: needs multiple sub-groups, space, and a facilitator.

**Reverse-engineer a few shopping-list items** - Intent: validate and reframe an existing feature list by mapping a few items, then push for alternatives. When to use: when a requirements list already exists. Trade-offs: reverse-engineering the entire list is a waste of time.

## Principles & best practices
- A milestone that hits the business goal with different scope is a success; the right scope that misses the goal is a failure.
- Put "Why?" at the center so everyone knows why they are doing the work.
- Goals are SMART, problem-focused, and money-linked; define good measurements before mapping.
- Aim for one objective per milestone where possible.
- Impacts are behavior changes, not features; show the change from current behavior; consider hindering impacts.
- Deliverables are options; keep them high level and refine iteratively; consider non-software alternatives.
- Find the shortest path; never aim to implement the whole map.
- Prioritize impacts, not deliverables.
- Visualize assumptions so you can test them and re-evaluate as you learn (pairs with build-measure-learn).
- Diverge then converge; do not criticize during ideation.
- Run with the right people (senior decision-makers plus technical experts) in an interaction-friendly room.

## Anti-patterns & pitfalls
Organizational mistakes: too many people in the preparation session (keep it to five or six); the wrong people (no decision-makers makes it pointless; no technical experts hides feasibility/risk; mid-level managers measured on a fixed feature set resist alternatives); poor room layout (theatre/classroom rows or a big boardroom table kill collaboration); presenting via projector/TV instead of printed handouts and writable surfaces.

Facilitation mistakes: criticizing ideas too early (kills divergent thinking before good alternatives emerge); one big group (lets a strong senior voice drive the agenda); reverse-engineering the entire shopping list (huge waste, especially with senior stakeholders); going into too much low-level detail too early (generating scope for all level-3 items is overkill).

Mapping mistakes:
- The Jumper - skipping levels, linking deliverables directly to actors and omitting impacts (breaks divergent thinking and hides assumptions).
- The Astronaut - goals and impacts with no good metrics ("weightless in space," no feedback on movement).
- The Surrealist - objectives with unrealistic or non-actionable metrics.
- The Shopper - going into too much deliverable detail early (unless scope truly must be fixed up front).
- (Implied) The fixed shopping list - insisting on mapping out every existing feature idea.

## Decision guidance
- Use impact mapping when the link between requested features and the business goal is unclear, when scope creep or "shopping list" planning threatens a project, or when planning a goal-oriented roadmap for iterative delivery.
- Always start from Why (the measurable goal), then Who, then How, then What; if a team jumps to deliverables, force them back up the levels.
- Constrain the goal to one measurable objective per milestone; if the people in the room cannot deliver it, change the objective or the people.
- Prioritize by impacts using natural discussion first, then dot-voting / virtual cash, optionally structured by Kano or purpose-alignment.
- Scope deliverables by "earn or learn": each is justified by a validated assumption or fits the learning budget; prefer the simplest support for an impact, including non-software options.
- Treat the map as a dynamic roadmap: re-evaluate assumptions and re-prioritize as delivery produces feedback; report progress as impacts achieved.

## Source attribution
- Definition of an impact map and the four questions/levels (goal, actors, impacts, deliverables) and their "getting it right" guidance: Impact Mapping, "What Is an Impact Map?" through the four-level sections (Adzic, 2012).
- Online gaming and financial-transaction examples: the examples sections.
- Role of impact maps (defining quality, roadmap management, scope creep, adaptive planning, build-measure-learn, iterative/incremental delivery, cost-to-investment): the "Role of Impact Maps" chapter.
- Three preparation steps and four mapping steps (discover goals, define measurements, plan milestone; draw skeleton, find alternatives, identify priorities, earn or learn): the "Creating an Impact Map" chapter.
- Organizational, facilitation, and mapping mistakes: the "Typical Mistakes" chapters.

## Recent developments (2024–2026)

- **"Outcomes over outputs" is now the default product orthodoxy, tightly coupled to OKRs.** 2025-2026 writing treats the output/outcome distinction (an output is what you build; an outcome is the change it produces) as foundational and pushes teams to tie roadmap decisions directly to company OKRs. This is exactly the stance impact mapping encodes (a milestone that hits the goal with different scope is a success; the requested scope that misses the goal is a failure). The vocabulary has standardized around input/output/outcome/impact key results. (What Matters, "Output vs. outcome with OKRs"; Product School, "Product OKRs"; Mooncamp, 2026)

- **Opportunity Solution Trees have become the most-cited outcome-mapping companion.** Teresa Torres' OST (outcome → opportunities → solutions → experiments) is now frequently paired with or contrasted against impact mapping as the go-to way to turn an outcome/OKR into testable work via empowered trios. Both share impact mapping's core move: measure success by a shifted metric rather than a shipped backlog. (Manuel Krueger, "From Outputs to Outcomes: How Opportunity Mapping Turns Product Discovery into Real Results," 2025; Product School)

- **Outcome-based roadmaps are mainstream practice.** The 2025 emphasis on roadmaps organized by outcomes/problems rather than feature lists, and on continuously re-evaluating against OKRs, operationalizes impact mapping's "deliverables are options, find the shortest path" and "report progress as impacts, not features" guidance across the wider PM community. (Meegle, "Product Manager Goals in 2025"; Businessmap Knowledge Base, 2025)

- **AI assists the mapping and synthesis, not the judgment.** AI is increasingly used to cluster feedback into candidate opportunities/impacts and to draft maps, but the senior-stakeholder, decision-maker-in-the-room facilitation that impact mapping depends on remains human work; the assumption-testing discipline (each deliverable justified by a validated assumption or a learning budget) is unchanged. (general 2025 product-discovery tooling commentary)

- **Method stable.** Adzic's Why → Who → How → What structure, the actor types, behavior-change impacts, deliverables-as-options, "earn or learn" scoping, and diverge-and-merge facilitation are unchanged and remain a strong fit for the current outcome/OKR era. The 2024-2026 development is the broad mainstreaming of outcome thinking (and the rise of the OST as a sibling tool), not a change to impact mapping itself.
