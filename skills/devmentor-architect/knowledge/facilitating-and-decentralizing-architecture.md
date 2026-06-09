# Facilitating and Decentralizing Architecture

**Domain:** 04 - Software Architecture Fundamentals
**Primary sources:** Facilitating Software Architecture (Harmel-Law, 2024)

## Overview
Traditional architecture practice puts a fixed group of named architects at the center of every significant decision - whether as remote "ivory tower" approvers or as "hands-on" architects who must personally bless each choice. In a world reshaped by five revolutions (Agile, cloud, DevOps, product thinking, and stream-aligned teams), that centralization breaks down: it blocks delivery flow, fails to incorporate enough feedback, and is fundamentally incompatible with decentralized teams and intentionally decentralized software. The architect becomes a bottleneck who is simultaneously accountable for everything and unable to know enough to decide well.

Facilitating software architecture means decentralizing architectural decision-making so that *anyone* can make and take a decision, while keeping the practice coherent and trustworthy through a small set of supporting elements. The keystone is the *architecture advice process*: a fast, decentralized decision method built on one rule, two advice-offering groups, and a social contract. It is reinforced by ADRs (the record and flow medium), an architecture advice forum (a transparent cadence for advice), and a "minimum viable agreement" expressed as testable cross-functional requirements, technology strategy, and collectively sourced principles. The aim is governance without gatekeeping: maximum decentralization of decision rights with just enough alignment to keep the system coherent - and a culture of trust and learning carefully nurtured into the space this opens up.

## Core concepts

**The architecture advice process (one rule).** Anyone - a team member or a cross-team architect - may take and communicate an architectural decision, *provided that during option-making they seek advice from* (1) everyone who will be meaningfully affected by the decision, and (2) people with expertise in the area. Seeking advice is mandatory; following it is not. This is the entire rule.

**Two advice-offering groups.** *Affected parties* bring "outside but here-and-now" context (the teams whose work or freedom changes). *Experts* bring "farsight" - past experience and wider horizons (longer time frames, broader scope, risk awareness, cost implications, organizational/market understanding). A single decider (or one team-sized group) initiates and is accountable; everyone in the sociotechnical system may play either role on any given decision.

**The social contract.** "We trust others to make and take decisions, on the understanding that they put in the hard work, listen, and think about the impact on everyone else. We in turn trust that others will do the same for us." Trust is built by involving the right people, listening to and understanding their advice, taking it into account, and sharing the final decision appropriately.

**Fast and decentralized.** The process is *fast* because exactly one party is accountable for taking the decision and *zero* parties can veto or block it - avoiding the need for consensus while reaping the speed of autocratic/consultative methods. It is *decentralized* because the "center" forms around whoever needs it, whenever and wherever needed; no one needs permission to initiate a decision. On Harmel-Law's speed/decentralization graph it sits uniquely in the top-right quadrant (fast *and* decentralized).

**Advice is not opinion.** Advice = suggested direction *plus reasoning*. Opinion is a "near enemy" of advice: it gives the direction ("use this") without the "why," so the decider cannot judge whether the reasoning applies to their context. Architectural decisions are always "it depends" decisions; advice helps reveal what they depend on. When you receive opinion, probe with "why"/"how" to turn it into advice.

**Accountability follows the decision, not the advice.** Offering advice does not make you accountable; *taking* the decision does. This is the crux of the power shift: teams that initiate a decision are now accountable for it (and its implementation), and cross-team architects remain accountable for system-wide decisions they initiate - even though teams always do the implementing. This frees advice seekers to listen deeply instead of defending, because they retain all the power to decide.

## Frameworks & models

**Taxonomy of decision processes (the problem the advice process solves).** Harmel-Law surveys standard methods along two axes - decentralization of decision rights and speed to respond:
- *Centralized:* **autocratic** (one person makes and takes; fast but a bottleneck and single point of failure), **delegation** (autocrat hands the decision to a better-placed individual; still a bottleneck), **consultative** (decider seeks input but is not obliged to use it; can shut out valid voices).
- *Decentralized:* **consent** (one decides, but anyone can veto/"paramount objection"; open to obstruction), **democratic** (one makes options, the group votes; the majority can ignore valid minority concerns), **consensus** (everyone involved in making and taking; maximally decentralized but slowest, prone to lowest-common-denominator compromise and to silently excluding people).
- *Avoidant* (deciding not to decide - itself a choice; valid as "last responsible moment" but you cannot build on never deciding).
None occupy the desirable "fast and decentralized" quadrant - which is exactly where the advice process lands.

**The three decision stages.** *Option making* → *decision taking* → *decision sharing*. Traditional approaches under-expose teams to the option-making stage. The advice process loads the social effort into option-making (seek advice) and makes sharing cheap (the people who decide are the people who implement, and advisers already have the context).

**Minimum viable agreement (MVA).** Just enough shared agreement to align with the organization's vision and goals - and no more, so team freedom is preserved. MVA is expressed through four complementary "means of agreement," ranging from most-specific/least-directive to least-specific/most-directive:
- **Cross-functional requirements (CFRs)** - most specific, least directive; say *what* the collective system must do, not *how*. Subtypes: performance, capacity/scalability, availability/reliability, security, legal/regulatory, accessibility/usability, monitorability/observability, operability, cost, integrability/extensibility. Made testable in the format "As a [role] I want [action] so that [value]" with "Given/When/Then" acceptance criteria, runnable in pipelines or production and even added to a team's definition of done.
- **Architectural principles** - say *how* you collectively intend to design; the embodiment of tech strategy in decision-guiding form.
- **Technology strategy** - most directive, least specific; the high-level "how."
- **Guidelines** (e.g., a technology radar) - fill the commonly asked blanks.
You are "aligned when you're not surprised."

**Characteristics of good architectural principles.** Each links to a strategic goal, has a memorable name and a call-to-action subtitle, states a rationale and explicit implications, says *how* (not *what*), and is **S.M.A.R.T.** (Specific, Measurable, Achievable, Realistic, Testable) so it can be used to evaluate options. Keep 8–15 principles - few enough to remember, enough to cover key commitments. Principles may be in healthy *tension* (e.g., "value team independence" vs. "'cloud native' means our cloud") but should not flatly contradict. Near enemies: conflating principles with *practices* (TDD, trunk-based delivery, pairing are practices, not architectural principles) or with non-architectural/CFR concerns in disguise. Source them collectively (a facilitated principles workshop with dot-voting) so they are not imposed.

**Coalescent argumentation (the social model for advice).** Architectural interactions default to adversarial, hierarchical *argument* (win/lose, "pull rank," "do we all agree?"). *Coalescent argumentation* (Gilbert) is cooperative: it asks not "What *can* I disagree with?" but "What *must* I disagree with?" - finding the shared middle ground first, then focusing creative energy on the genuine points of difference. ADRs operationalize this: the Context maps the agreed landscape, the Advice section isolates divergence, and the Consequences develop alternatives. This is the interaction style the advice forum must protect.

## Patterns

**Architecture Advice Process** - *Intent:* let anyone make and take architectural decisions fast, while keeping them well-informed, by mandating advice-seeking (not permission-seeking) from affected parties and experts. *When to use:* decentralized, fast-moving, multi-team organizations where centralized architects bottleneck flow. *Trade-offs:* requires a culture of trust and people willing to take accountability (not everyone is ready); architects must let go of control; reckless deciders are checked only by explicit ADR accountability and the social contract, not by approval gates.

**Architecture Advice Forum** - *Intent:* a regular, open, cadenced meeting where in-flight decisions (as ADRs) are presented and advice is sought and offered transparently, in front of an audience. *When to use:* to optimize the advice process by deduplicating advice, surfacing coordination needs, and catalyzing trust and group learning. *Trade-offs:* it is *not* an approval board (no signoff, no consensus); it can regress into an adversarial ARB if coalescent norms are not actively nurtured. *Standing agenda:* advice conversations on each new Draft/Proposed ADR; a timeboxed revisit of other statuses; AOB. *Differs from ARB/ADF in three ways:* no approval, wider attendees (a delegate per team plus experts: UX, product, data, ops, InfoSec, compliance, risk, sometimes execs), and conversations happen in the open. Benefits from many attendees even if most only observe and learn.

**ADRs as Flow** - *Intent:* use immutable, reader-focused ADRs both as the working medium of the advice process (seek advice in the Advice section, shape options/consequences as advice arrives) and as the architecture's historical change log. *When to use:* always, as the complement to the advice process. *Trade-offs:* discipline to record all advice and keep records immutable (supersede, never edit). (See the ADRs and decisions topic file for full structure and lifecycle.)

**Minimum Viable Agreement (CFRs + Strategy + Principles)** - *Intent:* provide just enough alignment to keep decentralized decisions coherent without imposing top-down control. *When to use:* when teams need to pull in the same direction but you want to protect their autonomy. *Trade-offs:* too much agreement curbs freedom; too little lets conceptual integrity erode; requires ongoing curation as decisions feed back into principles and CFRs.

**Collectively Sourced Principles** - *Intent:* generate decision-guiding architectural principles from a broad cross-section of people via a facilitated workshop. *When to use:* establishing or refreshing MVA. *Trade-offs:* workshop facilitation effort; principles must stay S.M.A.R.T. and be retired/updated (via a more rigorous ADR process) as they conflict with repeated decisions.

## Principles & best practices
- **Decentralize execution, centralize coordination** (Reinertsen). The advice forum is the place to do both: face-to-face, high-bandwidth coordination (Principle D22) lets teams realign peer-to-peer (D13), with "virtual centralization" (D5, "tiger teams") emerging from the affected teams themselves rather than by diktat.
- **Centralize only where it genuinely pays.** Deep, fast-changing domain expertise (InfoSec, legal/regulatory compliance, audit) should attend advice forums to *interpret* decisions in real time (Reinertsen's Scale Principle D2: centralize control for problems that are infrequent, large, or have economies of scale). You cannot reduce such expertise to static CFRs.
- **Seek advice from the right - and conflicting - people.** Do not seek advice only through established hierarchical relationships; deliberately include conflicting views. Make who is accountable for each decision explicit.
- **Sharper decisions reduce the advice burden.** Because more affected parties means more advice to seek, the process naturally rewards tighter decision scope, better system boundaries, and decoupling.
- **Transparency builds trust; cadence reinforces it.** Disseminate in-flight decision information widely (D17). Predictable, frequent, low-overhead "cadenced meetings" (F19) make decisions hard to drift far off course and act as a trust-building *ritual* (a participative recollectivization, distinct from hollow "ceremony"). Trust is built by being able to predict others' behavior under pressure (D23).
- **The advice process is compatible with enterprise frameworks.** Modern TOGAF/O-AA explicitly move to "align decision-making authority with accountability" and express policy intent rather than tasks - exactly what advice process + ADRs deliver, with an audit trail.
- **Start small, and start with whatever power you have.** Roll out the process incrementally; if you have decision power, model the behavior; if not, ask to use it for your own decisions first.
- **Curate the record.** Keep ADRs in developer tooling, with a reverse-chronological change-log page, highlighted "key" ADRs at forks in the road, and links to applicable principles for point-in-time context.

## Anti-patterns & pitfalls
- **Ivory-tower and hands-on bottlenecks** - both keep a fixed center of named architects who must approve everything; both block flow and starve decisions of feedback.
- **Opinion masquerading as advice** - direction without reasoning; often an unconscious attempt to retain power. Probe for the "why."
- **Architects unable to let go** - clinging to accountability they no longer hold, offering opinions instead of advice, or steering every ADR to "so, do we all agree?"
- **Turning the advice forum into an ARB** - re-introducing approval, consensus-seeking, rank-pulling, and adversarial argument; the forum's greatest strength (synchronous group interaction) is also its greatest risk.
- **Enlisting numbers to override a decider** - advisers ganging up to force their advice; counter by coalescing on the specific point of reasoned disagreement.
- **Assuming a culture of trust will appear** - culture rushes in to fill the space the advice process opens; it can be fear and information-hoarding rather than trust unless deliberately nurtured. Over-restricting with too many supporting elements smothers trust just as too few lets it fail to grow.
- **Bad principles** - too vague to evaluate options, too many to remember, conflating principles with practices, or being CFRs in disguise; principles imposed top-down rather than collectively sourced.
- **Single-option / unrecorded advice** - failing to record all advice (welcome or not) and losing the learning and trust dividends.

## Decision guidance
An agent advising on how to *run* architecture (rather than make a single decision) should:
1. **Diagnose centralization.** If named architects must approve significant decisions and this blocks flow or starves feedback, recommend moving toward the advice process.
2. **Apply the one rule.** For any significant decision, identify the affected parties and the experts, seek advice (direction + reasoning) from both, ensure it is understood, then let the single decider take and share the decision. Following advice is optional; seeking it is not.
3. **Keep accountability with the decider.** Whoever initiates the decision is accountable; advisers are not. Make this explicit in the ADR's author field.
4. **Prefer coalescent argumentation.** Frame conversations around "what *must* I disagree with," find common ground first, and isolate genuine differences for creative resolution.
5. **Add supporting elements deliberately and minimally.** Introduce an advice forum to make the process transparent and to coordinate; capture an MVA as testable CFRs, a technology strategy, and 8–15 S.M.A.R.T. collectively sourced principles. Check motivation before adding anything - protect the open space for trust.
6. **Centralize selectively.** Bring deep, fast-changing expertise (security, compliance) into the forum to interpret decisions in real time; let cross-team coordination emerge from the affected teams rather than by hierarchy.
7. **Use ADRs as the flow medium and the change log.** Record context, options, consequences, and all advice; supersede rather than edit; curate the collection as the system's history.

## Source attribution
- *Five revolutions, ivory-tower vs. hands-on critique, decentralization rationale, sociotechnical framing:* Facilitating Software Architecture (Harmel-Law, 2024), Chapter 1.
- *Decision as the unit of work, architecturally significant decisions, the three decision stages:* Chapter 2.
- *Taxonomy of decision processes (autocratic → consensus, avoidant) and the speed/decentralization graph:* Chapter 3.
- *The architecture advice process: one rule, two advice-offering groups, social contract, advice vs. opinion, accountability follows the decision, the two worked stories:* Chapter 4.
- *Rolling out the advice process, seeking advice from the right and conflicting people, confidence concerns:* Chapter 5.
- *ADRs as the record and flow medium (structure, immutability, lifecycle, management, curation):* Chapter 6.
- *Replacing hierarchy with decentralized trust, compatibility with TOGAF/O-AA, accountability as a check on recklessness, nurturing a culture of trust:* Chapter 7.
- *The architecture advice forum: agenda, differences from ARB/ADF, social dynamics, coalescent argumentation, Reinertsen flow principles (decentralize execution / centralize coordination, virtual centralization, cadence as ritual, transparency for cohesion):* Chapter 8.
- *Minimum viable agreement, testable CFRs, technology strategy, four means of alignment:* Chapter 9.
- *Collectively sourced architectural principles, S.M.A.R.T. criteria, principles vs. practices vs. CFRs, principles workshop:* Chapter 10.
- *Technology radar as a guideline mechanism:* Chapter 11.

## Recent developments (2024–2026)

- **The book landed and the advice process became a recognized practice.** *Facilitating Software Architecture* was published by O'Reilly in late 2024, and through 2025-2026 the architecture advice process moved from a single author's proposal to a widely discussed industry practice, covered by Thoughtworks, InfoQ, and the microservices.io community as a mainstream answer to "who should make architectural decisions." (InfoQ, "Decentralizing Architectural Decisions with the Architecture Advice Process," 2026, https://www.infoq.com/news/2026/03/architecture-advice-process/; Thoughtworks, "Software architecture decisions: who should be involved?," https://www.thoughtworks.com/en-us/insights/blog/architecture/software-architecture-decisions-andrew-harmel-law)

- **Explicit alignment with fast flow / Team Topologies.** A 2025 treatment by Chris Richardson connects the advice process to "fast flow," framing it as the decision mechanism that lets stream-aligned teams move quickly without an ivory-tower bottleneck while still staying coherent. This positions the advice process as the governance complement to Team Topologies rather than a standalone idea. (microservices.io, "Andrew Harmel-Law architecture advice process," 2025, https://microservices.io/post/architecture/2025/03/23/andrew-harmel-law-architecture-advice-process.html)

- **Tension with AI-agent governance.** As AI coding agents and agentic execution spread (2025-2026), an open question for the advice process is how the "anyone can decide after seeking advice" model and ADR-as-flow medium extend to non-human deciders; the broader trend is to push architecture intent into machine-checkable form (Architecture as Code, ADR enforcement tools) so decentralized human decisions and AI agents share the same guardrails. (InfoQ, "Where Architects Sit in the Era of AI," 2025, https://www.infoq.com/articles/architects-ai-era/)

- **Conceptually stable.** The one rule, two advice-offering groups, social contract, advice-vs-opinion distinction, minimum viable agreement, and coalescent argumentation are all as published in the 2024 book; 2025-2026 activity is adoption, integration with fast-flow thinking, and reconciliation with AI-assisted decision-making rather than revision of the model.
