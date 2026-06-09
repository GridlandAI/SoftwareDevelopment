# Continuous Discovery

**Domain:** 03 - Product Discovery and Planning
**Primary sources:** Continuous Discovery Habits (Teresa Torres, 2021)

## Overview
Continuous discovery is a structured, sustainable way for the team that builds a product to keep its decisions infused with customer input, week after week, rather than doing research once at the start of a project. Torres splits product work into discovery (deciding what to build) and delivery (building and shipping it), and argues that most organizations over-invest in delivery while under-investing in whether they are building the right thing. Continuous discovery corrects that imbalance, and it sits at the front of and runs alongside the entire SDLC because a digital product is never "done."

The method is anchored on one visual artifact, the opportunity solution tree, and a set of habits practiced by a small cross-functional decision-making unit, the product trio. The throughline is a shift from outputs (features shipped) to outcomes (impact on customers and the business), reached by exploring the opportunity space before jumping to solutions, and de-risking solutions by testing assumptions rather than whole ideas.

## Core concepts

**Discovery vs. delivery.** Discovery is the work of deciding what to build; delivery is building and shipping it. The two interweave but require different work. Marty Cagan's "delivery teams" only ship what they are told; continuous-discovery teams co-create with customers.

**The product trio.** A product manager (business viability), a designer (usability, systems and interaction design), and a software engineer (technical feasibility) making product decisions together. "Trio" can be a quartet or quintet, but more people slows decisions, so balance inclusiveness against speed. The same trio working on the same outcome over time matters because every change incurs a "learning tax."

**Working definition of continuous discovery.** (1) At a minimum weekly touchpoints with customers, (2) by the team building the product, (3) where they conduct small research activities, (4) in pursuit of a desired outcome. All four lines matter; research is not for its own sake but in service of an outcome.

**Outcomes vs. outputs.** An output is what you ship (a feature); an outcome is the impact it has. Three levels: business outcomes (revenue, cost, market share; often lagging indicators), product outcomes (how well the product moves the business forward; within the trio's span of control; usually a leading indicator), and traction metrics (usage of one specific feature/workflow). Assign product outcomes to mature trios for latitude; assign traction metrics only to junior trios learning discovery or to optimization teams on a known, validated behavior.

**Opportunities and the opportunity space.** "Opportunities" = customer needs, pain points, and desires (Torres avoids "problems" because some products serve desires, not fix problems). The opportunity space is the problem/desire space; it is infinite, which is why reaching an outcome is an "ill-structured" / "wicked" problem whose framing drives the solution.

**Assumptions.** Solutions rest on hidden assumptions across five categories: desirability (does anyone want it?), viability (should we build it for the business?), feasibility (can we build it technically/legally?), usability (can customers use it?), and ethical (could it cause harm?).

## Frameworks & models

**The Opportunity Solution Tree (OST).** A visual map of the paths to a desired outcome:
- Root: the desired outcome (business value).
- Opportunity space: customer needs, pain points, desires that, if addressed, drive the outcome (filtered to only those that could move the outcome).
- Solution space: solutions being explored under target opportunities.
- Assumption tests: experiments evaluating which solutions create customer value in a way that drives business value.

Benefits: resolves the tension between business and customer needs; builds and maintains shared understanding across the trio; supports a continuous mindset (break big project-sized opportunities into smaller leaf opportunities you can deliver one at a time); unlocks better decisions; unlocks faster learning cycles; builds confidence about what to do next (tree shape signals where to focus); simplifies stakeholder management ("show your work").

**The underlying structure of discovery.** Define a clear outcome (sets scope) → discover and map the opportunity space (frames the ill-structured problem) → discover solutions that address opportunities → test assumptions. Best teams work both top-down and bottom-up, evolving the whole tree continuously.

**Outcome setting as a two-way negotiation.** The product leader brings the across-business view and communicates strategic intent via an outcome (not a solution); the trio brings customer/technology knowledge and communicates how far it can move the metric in the period (usually a quarter). For a brand-new metric, start with a learning goal (discover what moves it) before a S.M.A.R.T. performance goal. Pair the primary outcome with health metrics so you do not optimize one metric to the detriment of all else (the Wells Fargo cautionary tale).

**Experience mapping.** Before diving into the infinite opportunity space, take inventory of what you know by mapping the customer's current experience. Trio members map individually first (avoid groupthink), then co-create a shared visual map. Experience-map nodes become the distinct top-level branches of the OST.

**Continuous interviewing.** Interview at least weekly to discover opportunities (not to ask customers what to build). People answer direct questions about their behavior unreliably (the jeans example; the "passive vs. active candidate" failure; Gazzaniga's "left brain interpreter"; Kahneman: confidence reflects coherence, not truth). So separate research questions (what you want to learn) from interview questions (what you ask). Elicit specific stories about actual past behavior ("Tell me about the last time you...") rather than generalities. Excavate the story with temporal prompts ("What happened first/next/before that?"), set expectations that the participant should give the full story, and gently steer them from generalizations back to the specific instance. Tailor story scope: narrow to optimize the product, broad to find new opportunities or markets. Let the customer talk about what matters most to them. Synthesize as you go with a one-page interview snapshot per interview; draw the stories.

**Mapping the opportunity space.** Capture opportunities from interview snapshots only if each is (1) framed as a customer need/pain/desire, not a solution; (2) seen in more than one interview (or relevant); (3) able to drive the outcome. Use a tree, not a flat backlog, because opportunities differ in size and interrelate. The tree encodes parent-child (subset) and sibling (similar but independently addressable) relationships. Identify distinct, non-overlapping top-level branches (from the experience map or from interview drawings). Add structure per branch: group similar opportunities, merge duplicates, add implied parents, build mini-trees, ladder up. Aim for "just enough structure." Choose leaf opportunities so you can deliver value iteratively.

**Prioritizing opportunities, not solutions.** Avoid "the build trap" (Melissa Perri); strategy lives in the opportunity space. Focus on one target opportunity at a time (limits work-in-progress, kanban-style; sets up compare-and-contrast). Use the tree to reduce assessment work: compare and contrast top-level opportunities, pick the best branch, then recurse down to a leaf. Assess each set with four lenses: opportunity sizing (how many customers, how often), market factors (table stakes vs. differentiator, external trends), company factors (vision, strategy, strengths, politics), and customer factors (importance vs. satisfaction). Embrace the messiness; make relative, data-informed subjective comparisons rather than a false-precision scoring formula.

**Supercharged ideation.** Quantity drives quality (fluency correlates with flexibility and originality; best ideas come late). Group brainstorming underperforms individuals working alone (social loafing, conformity, production blocking, downward norm-setting; the "illusion of group productivity"). Use the proven pattern: ideate alone → share with the team → ideate alone again → repeat until 15-20 ideas for the target opportunity. Get unstuck via breaks/incubation, analogous products from distant industries, extreme users, and wild ideas. Evaluate as a group via dot-voting down to three diverse ideas (groups evaluate better than individuals); do not narrow to one yet.

**Identifying hidden assumptions.** Make ideas concrete by story mapping (assume the solution exists; identify actors; map the steps each actor takes to get value; sequence horizontally over time). Then walk each step to generate desirability/usability/feasibility assumptions (often 20-30 per idea). Supplement with a pre-mortem (prospective hindsight phrased as certain failure: "it failed, what went wrong?") and by walking the lines of the OST for viability assumptions. Phrase assumptions so you need them to be true; be specific and testable.

**Assumption mapping (David J. Bland).** Plot each assumption on importance (y) vs. amount of evidence (x). Test the two or three "leap of faith" assumptions in the top-right corner (most important, weakest evidence). Go fast; positions are relative.

**Testing assumptions, not ideas.** Test assumptions across the set of three ideas in parallel (guards against confirmation bias and escalation of commitment; shared assumptions make testing efficient). A good test simulates the smallest relevant moment and evaluates behavior, not opinion. Define success criteria upfront and numerically ("at least 3 of 10 choose sports"), agreed by the team, to avoid ambiguous results and confirmation bias. Start with small, fast tests for early signals before scaling to larger experiments (smoke-screen tests, A/B tests). Most learning comes from failed tests; small tests let you fail faster. Account for false positives and false negatives by selecting for variety and by triangulating across multiple shared assumptions.

## Patterns

**Opportunity Solution Tree** - Intent: visualize and continuously evolve the paths from a business outcome through customer opportunities to tested solutions. When to use: any team adopting outcome-driven, continuous discovery. Trade-offs: requires real critical-thinking effort and ongoing maintenance; not a one-time artifact.

**Product trio** - Intent: make cross-functional product decisions collaboratively, keeping problem and solution space intertwined. When to use: building or evolving a digital product. Trade-offs: requires organizational support and a stable team; adding members slows decisions.

**Story-based interviewing** - Intent: learn actual customer behavior by collecting specific past stories. When to use: weekly continuous interviewing to discover opportunities. Trade-offs: feels inefficient; you do not always get the story you want; takes practice.

**Assumption mapping + leap-of-faith testing** - Intent: de-risk ideas by testing only the most important, least-evidenced assumptions. When to use: after ideation, before committing to build. Trade-offs: subjective, relative placement; risk of missing assumptions you never generated.

**Compare-and-contrast (avoid "whether or not")** - Intent: counter confirmation bias by always evaluating a set of options against each other. When to use: every prioritization and selection decision. Trade-offs: requires generating multiple options, which takes effort.

**Two-way-door (reversible) framing** - Intent: move fast on reversible decisions and reserve caution for irreversible ones (Bezos Level 1/2). When to use: most discovery prioritization decisions. Trade-offs: requires honest classification of reversibility.

## Principles & best practices
- Begin with the end in mind: start from a clear outcome, not a feature.
- Pursue business value by creating customer value; elevate customer needs to par with business needs.
- Weekly customer touchpoints by the building team, in pursuit of an outcome.
- Frame the opportunity space before solving; framing drives solutions.
- Prioritize opportunities, not solutions; work one target opportunity at a time (limit WIP).
- Compare and contrast; avoid "whether or not" decisions.
- Treat discovery decisions as reversible (two-way doors) to keep critically evaluating them.
- Balance confidence with doubt; act on what you know while preparing to be wrong.
- Ideate alone, share, ideate again; push past first ideas; dot-vote to a diverse set of three.
- Test assumptions, not whole ideas; test sets in parallel; define numeric success criteria upfront.
- Start small and iterate to bigger experiments; let failed tests kill flawed ideas cheaply.
- Show your work to stakeholders via the tree.

## Anti-patterns & pitfalls
- Choosing an output as an outcome ("launch an Android app" vs. "increase mobile engagement").
- Pursuing too many outcomes at once; ping-ponging outcomes quarter to quarter; setting individual instead of trio outcomes; focusing on one metric to the detriment of all else.
- Asking direct questions about behavior; multi-page discussion guides; not letting the customer lead.
- Flat opportunity backlogs; opportunities framed from the company's perspective; vertical (single-child) chains; opportunities with multiple parents; vague opportunities; solutions disguised as opportunities; capturing feelings instead of their causes.
- Mathematizing a messy prioritization into a false-precision score; analysis paralysis.
- One-session group brainstorming; falling for the illusion of group productivity; generating only variations of one idea; selecting ideas that do not address the target opportunity.
- Not generating enough assumptions; phrasing assumptions so you need them false; non-specific assumptions; favoring one assumption category and ignoring others (especially ethical).
- Rushing to test whole ideas instead of leap-of-faith assumptions; "some people" success criteria; testing with the wrong/biased audience; scaling experiments before an early signal.

## Decision guidance
- Decide outcome type by team maturity: product outcome for mature trios; traction metric only for junior trios or optimization of a validated behavior; for a brand-new metric, start with a learning goal.
- When overwhelmed by needs, map them onto an OST and prioritize branches top-down before assessing leaves.
- Assess opportunities through all four lenses (sizing, market, company, customer) as conversation fuel, not a formula; pick the highest-impact leaf for the current moment.
- After ideation, narrow to three diverse ideas; never to one before assumption testing.
- Prioritize assumptions by importance vs. evidence; test the top-right "leap of faith" ones across the idea set in parallel.
- Pick the smallest test that yields actionable data; scale only after an early positive signal; classify each decision as one-way or two-way door and pace accordingly.

## Source attribution
- Discovery vs. delivery, product trio, prerequisite mindsets, working definition: Continuous Discovery Habits, Ch. 1 (Torres, 2021).
- OST structure and benefits, outcome framing, ill-structured problems, four villains of decision-making, two-way doors: Ch. 2 and Ch. 7.
- Outcomes vs. outputs, business/product/traction outcomes, two-way negotiation, S.M.A.R.T. and learning goals, outcome anti-patterns: Ch. 3.
- Experience mapping: Ch. 4.
- Continuous/story-based interviewing, research vs. interview questions, interview snapshot: Ch. 5.
- Mapping the opportunity space, tree relationships, distinct branches, opportunity anti-patterns: Ch. 6.
- Prioritizing opportunities, four assessment lenses, the build trap, two-way doors: Ch. 7.
- Supercharged ideation, brainstorming research, dot-voting: Ch. 8.
- Five assumption categories, story mapping, pre-mortems, assumption mapping / leap-of-faith (Bland): Ch. 9.
- Testing assumptions not ideas, simulate-and-evaluate, numeric success criteria, small-to-large experiments, false positives/negatives: Ch. 10.

## Recent developments (2024–2026)

- **Torres' explicit guidance on AI in discovery.** Across 2024-2025 podcast appearances and Product Talk posts, Teresa Torres laid out where AI helps and where it harms continuous discovery. Her core warning: dumping an interview transcript into an LLM, reading the summary, and moving on captures only a fraction of the conversation. Her recommended workflow is to do your own synthesis first, then ask the AI, then compare the two so a second perspective surfaces what you missed, rather than outsourcing the thinking. She stresses that customer empathy and the discovery skills remain irreplaceable even as tooling improves. (Product Talk, "AI Evals & Discovery" and "Customer Interview Analysis," 2025; Lenny's Newsletter; Suprainsider, 2025)

- **"Building is cheap now, but don't build everything."** Torres' framing (airfocus/Lucid interview, 2025) captures the central tension AI introduces: because AI prototyping collapses the cost of producing a working artifact, the constraint shifts from delivery to discovery judgment. The risk is teams building everything because they can, rather than testing assumptions to decide what is worth building. This reinforces, rather than replaces, the opportunity-solution-tree discipline. (airfocus by Lucid, 2025)

- **AI compresses the test-assumptions loop.** Practitioner reports describe a discovery loop now run in roughly 24 hours: generate a concept, test it with a handful of users, feed results to AI for synthesis, refine the prototype, retest. Because deploying a working prototype to production is cheap, viability and desirability can be tested with real market signal rather than opinion, letting teams validate while assumptions are still cheap to change. This is a tooling acceleration of Torres' "test assumptions, not whole ideas," not a new method. (ProdPad, 2025; iCAgile, 2025; Productboard, 2025)

- **Torres shipped a production AI product (the Interview Coach).** After three months building full-time with AI, Torres released an AI-powered feedback tool that gives practice feedback on interviewing technique, replacing the live cohort course she had run since 2017. Notable as a discovery thought-leader dogfooding AI delivery and reporting on what the experience taught about AI products. (Product Talk; Lenny's Newsletter, 2025)

- **Stable core, evolving practice.** The opportunity solution tree, the product trio, weekly story-based interviewing, assumption mapping, and testing assumptions rather than ideas remain the canonical method and are unchanged since the 2021 book. The 2024-2026 developments are about AI tooling augmenting (not replacing) each habit, with consistent caution against letting AI synthesis substitute for the team's own sense-making.
