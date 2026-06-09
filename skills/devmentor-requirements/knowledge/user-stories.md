# User Stories

**Domain:** 02 - Requirements and Specifications
**Primary sources:** User Stories Applied: For Agile Software Development (Cohn, 2004); The Art of Crafting User Stories (Lee, 2023)

## Overview
Software requirements are fundamentally a communication problem between the people who want software and the people who build it. User stories are a lightweight technique for handling that communication: short, plain-language descriptions of functionality that is valuable to a user or purchaser of a system. Rather than capturing every detail up front in a contract-like document, stories defer detail until it is needed, spreading decision-making across the life of the project and keeping customers and developers in continuous conversation.

A user story is deliberately not a specification. It is a placeholder for a conversation and a promise that the team will work out the details together when the work is scheduled. This makes stories cheap to write, easy to reprioritize, and resistant to the "ping-pong" of formal requirements documents that two groups rewrite to position themselves for end-of-project blame. Stories underpin most agile methods (Scrum, XP, Kanban) and serve as the unit of planning, estimation, and acceptance.

## Core concepts

**The Card, Conversation, Confirmation model (Cohn, 2004).** Ron Jeffries' "three Cs" capture the three aspects of every story:
- **Card** - a written description (traditionally a hand-written note card) used for planning and as a reminder. The card is the most visible part but the least important; it represents requirements rather than documenting them.
- **Conversation** - the ongoing dialogue between the customer team and developers that fleshes out the details. The conversation is where the real requirements live.
- **Confirmation** - the acceptance tests that convey and document the details and determine when the story is complete. Often written on the back of the card.

**The standard story template.** The Connextra format, popularized by Cohn, embeds the user role and motivation:

```
As a <role>, I want <function> so that <business value>.
```

Cohn's original phrasing was "I as a (role) want (function) so that (business value)." Lee restates it as "As a [user], I want [goal], so that [reason]." The "so that" clause forces the writer to articulate value and helps distinguish important stories from frivolous ones. Stories should be written in active voice ("A Job Seeker can post a resume," not "A resume can be posted") and for a single user ("a Job Seeker can remove her own resumes") to surface hidden ambiguity.

**What is valuable.** Value can accrue to a *user* (someone who uses the software) or a *purchaser* (someone who buys it). A story like "All configuration is read from a central location" may be valued by a purchaser deploying to 5,000 machines even though users do not care. The thing to avoid is stories valued only by developers (for example, "All database connections go through a connection pool"); these should be reframed in terms of user or purchaser value (for example, "Up to fifty users can use the application with a five-user database license").

**Epics and themes.** An *epic* is a story too large to implement directly; it serves as a placeholder for a big part of the system and can be assigned a large, rough estimate. Epics are split into smaller stories when their implementation horizon approaches. A *theme* is a collection of related stories. Both let teams write at different levels of granularity.

**User roles and personas (Cohn, 2004).** Before writing stories, identify the *user roles* - collections of defining attributes that characterize a population of users (for example, Job Seeker, Recruiter, First Timer). Role modeling steps: brainstorm an initial set of role cards, organize them (overlapping roles near each other), consolidate and refine, and add role attributes. For the most important roles, create a *persona* - an imaginary, named, fleshed-out representative of the role - to keep developers thinking about real, tangible users.

**Acceptance criteria.** The specific conditions that must be met for a story to be considered done. They are captured as short test reminders (Cohn) and define the customer's expectations, answering "how do we know when we're finished?" In Lee's framing, acceptance criteria turn a story from a vague intent into something testable and verifiable, and feed directly into ATDD and BDD.

## Frameworks & models

**INVEST (Bill Wake, via Cohn 2004; reiterated by Lee 2023).** Six attributes of a good story:
- **Independent** - avoid dependencies between stories; they complicate prioritization and estimation. Resolve dependencies by combining stories or splitting along a different dimension.
- **Negotiable** - a story is not a contract; it is a reminder to converse. Include just enough detail; record settled details as tests, not as story-card clutter.
- **Valuable** - to a user or purchaser, not just to developers. Keep technology and UI assumptions out.
- **Estimatable** - the team must be able to size it. If they cannot, the cause is usually missing domain knowledge (talk to the customer), missing technical knowledge (run a timeboxed spike), or a story that is too big (split it).
- **Small** - small enough to schedule into an iteration; epics are split, tiny stories are combined.
- **Testable** - passing its tests proves completion. Rewrite untestable nonfunctional stories ("never wait long for a screen") into testable ones ("new screens appear within two seconds in 95% of cases").

**Story-writing workshops (Cohn, 2004).** Stories are best generated in a workshop where the whole customer team and developers brainstorm as many stories as possible. The customer team - not developers - owns story writing, for two reasons: stories must be in the language of the business so the customer can prioritize them, and the customer is the product visionary best positioned to describe desired behavior. Developers help by suggesting stories and doing the writing during the workshop.

**Story mapping (Lee, 2023).** A two-dimensional visualization. The horizontal *backbone* is the sequence of high-level user activities or goals (the user journey); beneath each activity, stories hang vertically, ordered by priority. The map gives a holistic, user-centric view of the whole product, helps identify the MVP, exposes gaps, and supports incremental release slicing.

**Estimation: story points and velocity (Cohn, 2004).** Stories are sized in *story points* - relative units (Cohn favors "ideal days," also called Nebulous Units of Time). A four-point story is roughly twice a two-point story. *Velocity* is the number of points completed per iteration; measured velocity from one iteration predicts future iterations (justified by the Central Limit Theorem - summed independent estimates trend normal). Conditions for extrapolating velocity: nothing unusual affected the iteration, estimates were generated consistently, and the stories were independent.

**Estimation techniques.** Cohn's team estimation is a Wideband Delphi variant (the ancestor of Planning Poker): developers privately write estimates, reveal simultaneously, the high and low estimators explain their reasoning, then re-estimate; repeat until convergence (rarely more than three rounds). *Triangulate* by comparing a new story against already-estimated ones pinned to the wall by size. Lee adds Planning Poker, T-shirt sizing (XS-XL), and the Bucket system, and notes the #NoEstimates movement (using cycle time, lead time, and throughput instead of point estimates).

**Prioritization frameworks (Lee, 2023).**
- **MoSCoW** - Must have / Should have / Could have / Won't have. Best when must-haves and nice-to-haves are clearly distinguishable and resources are constrained.
- **Value-based** - assign business value (ROI, retention, satisfaction) and sort. Best for maximizing value across many competing features.
- **WSJF** (Weighted Shortest Job First) - `(Business Value + Time Criticality + Risk Reduction + Opportunity Enablement) / Job Size`. Best for maximizing economic value and minimizing waste.
- **RICE** - Reach, Impact, Confidence, Effort. Best when reach and impact dominate (driving adoption or revenue).
- **Kano model** - classifies features by their effect on satisfaction. Best for differentiation and delight.

These are not mutually exclusive and can be combined.

## Patterns

Story-splitting patterns (Cohn 2004; Lee 2023). For each: intent; when to use; trade-offs.

**Slice the cake (vertical slicing)** - Intent: split a large story so each resulting story delivers end-to-end functionality through every architectural layer. When to use: whenever an epic must be split for an iteration. Trade-offs: harder than slicing by technical layer, but each slice is independently useful, exercises every layer (reducing late integration risk), and is potentially releasable. Avoid the anti-pattern of splitting into "fill out the form" and "save to the database," neither of which is useful alone.

**Compound-story split** - Intent: break an epic that is really several distinct stories into its constituents (for example, "post a resume" into create / edit / delete / multiple-resumes / activate-inactivate). When to use: the epic bundles clearly separable capabilities. Trade-offs: do not over-split into trivially small stories; group create/edit/delete sensibly.

**Split by data boundaries** - Intent: split along the data the story manipulates (for example, "add and edit education," "add and edit job history," etc.). When to use: a CRUD-style create story is still too large. Trade-offs: many similar small stories; choose the dimension that yields the most useful, schedulable chunks.

**Spike / investigation split (complex story)** - Intent: separate an inherently uncertain story into a timeboxed investigative spike plus the real development story. When to use: developers lack technical knowledge to estimate (for example, first-time credit-card processing). Trade-offs: lets the customer prioritize research separately; the spike has a maximum time budget; put the spike and the dependent work in different iterations to contain uncertainty.

**Combine tiny stories** - Intent: staple together stories too small to be worth tracking individually (bug fixes, minor UI tweaks) into one named story of roughly half a day to several days. When to use: a change would take longer to write down and estimate than to make. Trade-offs: the combined story is scheduled and estimated like any other.

**Split by workflow steps / business rules / operations / data variations (Lee, 2023)** - Intent: four reusable axes for breaking down a story: the sequence of steps the user follows, separate business rules or conditions, CRUD operations, or different data inputs/outputs. When to use: pick whichever axis yields independently valuable, similarly sized stories. Trade-offs: workflow splits risk producing non-closed fragments; business-rule splits keep stories cohesive.

**Constraint cards (Newkirk and Martin, via Cohn)** - Intent: capture requirements that must be *obeyed* rather than *implemented* (for example, "99.999% uptime," "must use the existing order database") on cards labeled "Constraint." When to use: nonfunctional requirements that span many stories. Trade-offs: not estimated or scheduled, but should be backed by automated tests run every iteration so the constraint is not silently violated.

## Principles & best practices

- **Start with goal stories.** For each user role, list the goals that role has for using the system; high-level goals generate the detailed stories beneath them (Cohn).
- **Write closed stories.** A closed story finishes with a meaningful, achievable goal, giving the user a sense of accomplishment ("a recruiter can change the expiration date of an ad," not "a recruiter can manage her ads") (Cohn).
- **Size to the horizon.** Write near-term stories small enough to schedule; leave distant work as large, imprecise epics (Cohn).
- **Keep the UI out as long as possible.** Mixing solution (UI) detail into requirements prematurely constrains design. UI detail legitimately enters only as the system matures (Cohn).
- **Include the user role; write in active voice; write for one user** (Cohn).
- **Don't number story cards;** use short titles instead - numbering adds overhead and abstracts away the tangible feature (Cohn).
- **Don't forget the purpose:** the card is a reminder to talk, not a substitute for the conversation. Keep it brief (Cohn).
- **Use a standard format and avoid ambiguity;** keep stories simple and free of jargon (Lee).
- **Always include clear, specific acceptance criteria;** use measurable conditions, focus on the end user, cover negative scenarios and edge cases, and write them collaboratively with stakeholders (Lee).
- **Some things aren't stories.** UI guidelines, external system interfaces, and similar artifacts may be better expressed in another format; use it when it fits (Cohn).
- **Refine and revisit continually.** Stories evolve through backlog refinement as understanding grows (Lee).

## Anti-patterns & pitfalls

- **Too much detail on the card.** Excess notes create the illusion of precision and discourage the necessary conversation; settled details belong in tests (Cohn).
- **Writing epics and calling them stories.** Large stories cannot be planned or estimated; break them down to fit a single iteration (Lee).
- **Splitting along technical layers** (UI story / database story) so that no single story is useful - violates "slice the cake" (Cohn).
- **Untestable stories,** especially vague nonfunctional ones ("easy to use," "never slow") - rewrite with measurable thresholds (Cohn).
- **Stories valued only by developers,** framed in technology rather than user benefit (Cohn).
- **Focusing on implementation (the "how") instead of the user's goal (the "what")** (Lee).
- **Missing or vague acceptance criteria,** leading to disputes over "done" (Lee).
- **Writing stories in isolation,** not involving users/SMEs, so stories drift from product goals (Lee).
- **Forgetting nonfunctional requirements** (performance, security, scalability) (Lee).
- **Failing to prioritize or to revisit stories** as needs change (Lee).
- **Dependencies between stories** that distort estimation and planning (Cohn, INVEST).

## Decision guidance

- **When detail is unknown, leave the story coarse.** Defer detail to the conversation at the moment work is scheduled. Add only enough annotation to resume the conversation later.
- **Choosing a split axis:** prefer the split that produces independently valuable, similarly sized, closed stories. Default to vertical slicing ("slice the cake"); if a create operation is small enough, split by CRUD; if not, split by data boundaries; if uncertainty dominates, carve out a timeboxed spike.
- **Combine vs. split:** if stories are dependent and the combined size is small (roughly under a week), combine them; otherwise find a different splitting dimension.
- **Choosing a prioritization framework:** MoSCoW for clear must/nice distinctions under constraint; value-based or WSJF for economic optimization; RICE for reach/impact-driven adoption goals; Kano for differentiation. Combine as needed.
- **Estimation unit:** treat story points as relative size (ideal days work well). Use measured velocity, not initial guesses, after the first iteration. Estimate as a team, never as an individual; triangulate against known stories.
- **Stories vs. formal specs:** prefer stories for evolving, collaborative, agile work; reach for a formal document only where a story format genuinely fails (interfaces, UI standards). Stories and requirements documents can coexist.

## Source attribution
- Card/Conversation/Confirmation, the customer team, the standard template, user roles and personas, INVEST, story-writing workshops, epics, story-splitting patterns (slice the cake, compound/complex split, spikes, combining, constraints), guidelines for good stories, story points / Wideband Delphi estimation / velocity / triangulation, acceptance tests, and "what stories are not" (vs. IEEE 830 and use cases): **Cohn, *User Stories Applied*, 2004** (Chapters 1, 2, 3, 7, 8, 9, 12).
- Story mapping, MoSCoW / value-based / WSJF / RICE / Kano prioritization, Planning Poker / T-shirt / Bucket estimation and #NoEstimates, acceptance-criteria best practices and definition of done, splitting by workflow/rules/operations/data, and the consolidated list of common mistakes: **Lee, *The Art of Crafting User Stories*, 2023** (Chapters 1, 3, 4, 5, 6, 7).

## Recent developments (2024–2026)
The user-story technique (Card/Conversation/Confirmation, the Connextra template, INVEST, story mapping, splitting patterns, and the prioritization frameworks) is stable. The active development is LLM involvement in writing, splitting, and assessing stories, with consistent evidence that human judgment stays in the loop.

- **LLMs can draft stories and acceptance criteria, with caveats.** 2024-2025 studies show LLMs generate user stories with high syntactic and stylistic quality and can produce acceptance criteria, technical details, and a definition of done from a feature description. But LLM-generated stories tend to pass acceptance checks less often than human-written ones, show lower diversity and creativity, and require product-owner validation to align with goals, so they augment rather than replace the customer-team-owns-stories principle. (arXiv 2507.15157 "Can LLMs Generate User Stories and Assess Their Quality?", 2025; arXiv 2404.01558, 2024)
- **LLMs as story-quality assessors.** Research found capable models (for example, Claude 3 Opus) can reliably assess the semantic quality of user stories when given explicit evaluation criteria, offering a way to triage large backlogs against INVEST-style qualities before human review. (arXiv 2507.15157, 2025; arXiv 2403.09442 "LLM-based agents for enhancing user story quality," 2024)
- **Prompt patterns and tooling for story writing have proliferated.** Practitioner prompt libraries and AI features in backlog tools now generate stories, split epics, and propose acceptance criteria from a goal statement, putting the splitting patterns (vertical slicing, CRUD, workflow, business-rule) into the prompt rather than only the whiteboard. The recurring guidance is to keep the human as editor and prioritizer. (Faqprime, 2025; vendor backlog tooling, 2025)
- **EARS and structured acceptance criteria feed AI builders.** Spec-driven development tools (Amazon Kiro) author requirements as EARS-format user stories with acceptance criteria precisely because structured, testable stories make better prompts for agentic code generation, raising the practical payoff of Lee's "always include clear, specific, measurable acceptance criteria." (Kiro, 2025; GitHub Spec Kit, 2025)
- **Estimation pressure from #NoEstimates and AI velocity.** With AI compressing implementation time, interest in flow metrics (cycle time, lead time, throughput) over point estimates continues to grow, consistent with the #NoEstimates direction Lee notes; teams increasingly treat story points as a conversation aid rather than a forecast. (industry discussion, 2024-2025)
