# Empirical Design, Refactoring, and Software Craft

**Domain:** 06 - Software Design Patterns and DDD
**Primary sources:** Tidy First? A Personal Exercise in Empirical Software Design (Kent Beck, 2023); The Software Craftsman: Professionalism, Pragmatism, Pride (Sandro Mancuso, 2014); Your Code as a Crime Scene, 2nd ed. (Adam Tornhill, 2024)

## Overview
This topic is about the human and economic side of software design: how to improve the structure of code in small, reversible steps (Beck), how to behave as a professional who keeps that structure healthy over a career (Mancuso), and how to find where structure has decayed by mining the behavioral and social signals in version control (Tornhill). The three perspectives reinforce each other. Beck supplies a theory of why design matters (coupling drives cost, structure creates options) and a discipline for acting on it (tidyings). Mancuso supplies the professional ethos and the technical practices (TDD, refactoring, CI, pair programming) that keep code changeable. Tornhill supplies the empirical, data-driven methods (hotspots, change coupling, complexity trends, organizational metrics) for prioritizing where to apply that effort across large codebases no individual can hold in their head.

The unifying claim across all three: **the cost of software is dominated by the cost of changing it, and the cost of change is dominated by coupling and by how people and code interact.** Good design is therefore an ongoing, empirical, economically-grounded, and fundamentally human activity, not a one-time up-front act.

## Core concepts

### Software design is "beneficially relating elements" (Beck 2023)
A design is the set of elements (tokens -> expressions -> statements -> functions -> objects/modules -> systems), the relationships between them (invokes, publishes, listens, refers), and the benefits those relationships create. Designers can only: create/delete elements, create/delete relationships, or increase the benefit of a relationship. Extracting `box.area()` from `box.width() * box.height()` creates a new element and simplifies the relationship between caller and box.

### Structure vs behavior (Beck 2023)
Software creates value two ways: **behavior** (what it does today) and **structure** (the options for what it can do tomorrow). The structure does not affect behavior. The key asymmetry: **behavior changes are often irreversible** (a wrong tax notice already sent), while **structure changes are generally reversible** (extract a helper, dislike it, inline it). Because structure changes are reversible and cheap to undo, they deserve far less ceremony than behavior changes; that is exactly why Beck calls them "tidyings."

### Coupling and Constantine's Equivalence (Beck 2023)
Two elements are **coupled with respect to a change** if changing one forces changing the other. Coupling must always be qualified by which change; coupling for a change that never happens does not matter. Coupling is 1-to-N and cascading, and cascading changes follow a power-law distribution where a few huge changes cost more than all the small ones combined. This yields Constantine's Equivalence:
`cost(software) ~= cost(change) ~= cost(big changes) ~= coupling`.
To reduce software cost, reduce coupling. But decoupling is not free, and reducing coupling for one class of changes tends to increase it for another, so do not chase zero coupling.

### Cohesion (Beck 2023)
Coupled sub-elements should live in the same containing element; uncoupled ones should move out. Improve an incohesive module either by extracting the coupled subset into a cohesive sub-element or by moving uncoupled elements to where their real siblings are. Make no sudden moves: work with incomplete information, move one element at a time, follow the Scout Rule.

### The economics: time value and optionality (Beck 2023)
Two financial truths reshape design judgment: (1) **a dollar today is worth more than a dollar tomorrow**, so earn sooner and spend later (this argues for tidy *after* over tidy *first*); (2) **in a chaotic situation, options beat things**, so create options under uncertainty (this argues for tidy *first* when it creates options). Software design reconciles these. The more volatile the value of a possible future change, the more valuable the option to make it, even before you exercise it. "Tidy First?" is the per-decision tug-of-war between discounted cash flow and option value.

### Software craftsmanship = professionalism (Mancuso 2014)
Software craftsmanship is a mindset and a long journey to mastery in which developers take responsibility for their own careers and put professionalism, pragmatism, and pride back into the work. The shortest definition: it is about professionalism in software development. It values not only working software but well-crafted software; not only responding to change but steadily adding value; not only individuals and interactions but a community of professionals; not only customer collaboration but productive partnerships.

### Behavioral / forensic code analysis (Tornhill 2024)
Static complexity metrics lack context and priority. Instead, treat version-control history as evidence: where developers actually spend effort reveals what matters. By analogy to geographical offender profiling, you compute a "probability surface" over the codebase to find the few modules worth attention, rather than patrolling the whole city. The core signals are **hotspots**, **complexity trends**, **change coupling**, and **organizational/social metrics**.

## Frameworks & models

### The tidyings catalog (Beck 2023, Part II)
Small, safe structure-only changes that make the next behavior change easier: Guard Clause; Dead Code (delete it); Normalize Symmetries; New Interface, Old Implementation; Reading Order; Cohesion Order; Move Declaration & Initialization Together; Explaining Variable; Explaining Constant; Explicit Parameters; Chunk Statements; Extract Helper; One Pile (inline an over-split mess before re-splitting); Explaining Comment; Delete Redundant Comment. Each is minutes-to-hours in size and reversible.

### Managing tidying (Beck 2023, Part III)
- **Separate Tidying** - put tidyings in their own pull requests, as few per PR as possible; keep structure changes out of behavior PRs. Review latency is an incentive loop: fast reviews enable smaller PRs; consider not requiring review for pure tidying PRs.
- **Batch Sizes** and **Rhythm** - keep batches small and interruptible.
- **First, After, Later, Never** - the timing decision:
  - **Never:** only when the code's behavior will truly never change again.
  - **Later:** keep a "Fun List" of messes; tidy them in spare slots. Requires believing there is enough time to do the work well.
  - **After:** tidy right after a behavior change when you will revisit the area soon, it is cheaper now, and the tidying is roughly proportional in size to the change.
  - **First:** tidy before the change when `cost(tidy) + cost(change after tidy) < cost(change without tidy)` (always do it then), or when the option value of tidying outweighs spending sooner, or simply as justified self-care.

### Reversibility as a decision frame (Beck 2023)
Treat reversible and irreversible decisions differently. Reversible (most structure changes): little downside, so invest little in avoiding mistakes. Irreversible (most behavior changes, "Extract as a Service", decisions that propagate everywhere): go slow, double-check, or first make them reversible (feature flags, prototypes in production, tidying so a decision touches few places).

### The Software Craftsmanship Manifesto and XP practices (Mancuso 2014)
- Manifesto (raising the bar): not only working software but well-crafted software; not only responding to change but steadily adding value; not only individuals and interactions but a community of professionals; not only customer collaboration but productive partnerships.
- Technical practices and the value (short feedback loop) each brings to the business: **automated testing** (system tested in minutes, deploy with confidence); **test first** (focus, small increments, just-enough code); **TDD** (a *design* practice that discourages overengineering/BDUF and surfaces complexity); **continuous integration** (with stop-and-fix, system always deployable); **pair programming** (immediate four-eye review, knowledge sharing, shared standards); **refactoring** (Boy Scout rule, mitigate broken-windows, but apply pragmatically to code that actually changes).
- Sell the *value* (smaller feedback loop, fewer bugs, knowledge sharing, fast releases), not the practice name.

### Hotspots (Tornhill 2024, Part I)
A **hotspot** is complicated code that you have to work with often: the overlap of **complexity** (a proxy such as lines of code) and **change frequency** (effort, mined from git). Research consistently shows change frequency predicts defects better than size alone, and hotspots correlate with defects and even security vulnerabilities. Hotspots are probabilistic pointers, not proof; confirm with code-level inspection. Use them to refactor the most expensive technical debt first.

### Complexity trends and Lehman's law (Tornhill 2024, ch. 5)
Lehman's law of increasing complexity: an evolving program's complexity increases (its structure deteriorates) unless work is done to maintain or reduce it. Track a hotspot's complexity over time (e.g., from indentation/shape, "negative space") to tell whether you are improving the code or contributing to the decline.

### Change (temporal) coupling (Tornhill 2024, ch. 8)
Modules are **change-coupled** if they repeatedly change together in commits, a temporal dependency invisible in a static snapshot. Two types: **intentional/explicit** (a file and its unit test) and **incidental/implicit** (two files with no code dependency that must change together, often a DRY violation or communication over a network/bus). Implicit dependencies drive cognitive load because they must be carried in the developer's head. Accidental complexity comes in two forms: complex individual modules (hotspots) and complex *interactions* (change coupling); both must be addressed.

### Organizational and social metrics (Tornhill 2024, Part III)
- **Brooks's Law** - adding people to a late project makes it later, because communication paths grow combinatorially while available hours grow linearly; past some point each added person is a net loss. Visualize it by plotting output-per-developer against team size over time.
- **Diffusion of responsibility** - larger groups mean each member feels less responsible; more code smells go unaddressed. The defensive move is to speak up and to not scale beyond what the codebase can sustain.
- **Conway's Law** - system structure mirrors the communicating organization that builds it. Used in reverse (and via Team Topologies), it becomes a design tool: align module boundaries with team responsibilities; analyze where communication dependencies actually are.
- **Truck/bus factor** - the minimum number of people who can leave before the system becomes unmaintainable through lost knowledge. Often only 1–2 maintainers hold most knowledge of a module; low truck factor is a key-person risk and a knowledge silo. Build a **knowledge map** from authorship data to see it.
- **Technical problems cause organizational problems and vice versa** - God Classes become "traffic jams" forcing many developers into the same file (coordination bottlenecks); bad code is a top cause of developer unhappiness and attrition, and each departure erodes the truck factor. Refactoring improves morale and retention, not just risk.

## Patterns
For each: name, intent, when to use, trade-offs. (Also appended to PATTERN-CATALOG.md.)

- **Tidying (small reversible structure change)** - Intent: make a tiny, safe, structure-only improvement that makes the next behavior change easier. When: just before, just after, or later relative to a behavior change, per the First/After/Later/Never decision. Trade-offs: cheap and reversible with compounding benefit; can become a procrastinating "tidying binge" if not bounded to the change at hand.
- **Separate Tidying (structure PRs apart from behavior PRs)** - Intent: keep structure changes in their own small pull requests. When: any team using code review. Trade-offs: reviewable, low-risk, faster reviews; risks "pointless PR" pushback, so keep each focused and consider skipping review for pure tidyings.
- **First/After/Later/Never decision** - Intent: choose when to tidy relative to a behavior change using economics. When: every messy change. Trade-offs: aligns design effort with cash flow and option value; requires judgment, not a formula.
- **Reversibility framing** - Intent: invest review/caution in proportion to how hard a decision is to undo, and make risky decisions reversible first. When: any consequential design or deployment decision. Trade-offs: avoids costly irreversible mistakes and wasted ceremony on cheap ones; requires honestly assessing reversibility and propagation.
- **Hotspot analysis** - Intent: rank modules by the intersection of complexity and change frequency to target refactoring. When: prioritizing technical-debt work in a large codebase. Trade-offs: lightweight, data-driven, defect-correlated prioritization; probabilistic, so confirm with inspection.
- **Complexity-trend analysis** - Intent: track a module's complexity over time to see if it is improving or decaying (Lehman's law). When: monitoring a hotspot you are trying to fix or guard. Trade-offs: shows direction of change cheaply; a proxy metric, not absolute truth.
- **Change-coupling (temporal coupling) analysis** - Intent: find files that change together in version control to reveal hidden/implicit dependencies. When: architectural review, finding DRY violations and entangled modules. Trade-offs: surfaces dependencies static analysis cannot; needs interpretation (intentional vs. incidental coupling).
- **Organizational/knowledge-map analysis** - Intent: mine authorship to find coordination bottlenecks, truck-factor risk, and Conway's-Law misalignments. When: scaling teams, planning ownership, de-risking key people. Trade-offs: makes invisible social risk visible; depends on clean commit/author data and respectful use.

## Principles & best practices
- The cost of software is approximately the cost of change, which is approximately coupling; reduce coupling, but accept that some coupling is inevitable and decoupling has its own cost. (Beck 2023)
- Separate structure changes from behavior changes; treat reversible structure changes lightly and irreversible behavior changes carefully. (Beck 2023)
- Tidy in small, interruptible steps and put tidyings in their own PRs; tidy first only when it lowers total cost or creates worthwhile options. (Beck 2023)
- Own your career: invest your own time in learning; behave as a professional in a productive partnership with the client, not as a factory worker. (Mancuso 2014)
- Keep software well-crafted so it grows more valuable, not more painful, with age; leave code cleaner than you found it (Boy Scout rule). (Mancuso 2014)
- Adopt TDD, CI with stop-and-fix, pair programming, and pragmatic refactoring to shorten feedback loops; back practices with shared values. (Mancuso 2014)
- Prioritize improvement with data: refactor hotspots first, watch complexity trends, untangle change-coupled modules, and monitor organizational metrics. (Tornhill 2024)
- Align module boundaries with team boundaries (Conway's Law as a design tool); manage truck factor before it becomes a crisis. (Tornhill 2024)

## Anti-patterns & pitfalls
- **Tidying too much** ("tidying binge") and delaying the behavior change others are waiting for. (Beck 2023)
- **Chasing zero coupling**, which simply moves coupling to another class of changes and wastes effort. (Beck 2023)
- **Treating all decisions with equal ceremony**, over-reviewing reversible tidyings while under-checking irreversible behavior changes. (Beck 2023)
- **Relying on the company to grow your skills** and doing only what you are told (factory-worker mindset). (Mancuso 2014)
- **Partial or inconsistent practice adoption** ("we do TDD sometimes"), which makes it impossible to judge benefits. (Mancuso 2014)
- **Rewrite trap** - rebuilding a system with the same bad techniques that ruined the original, often triggered by attrition that erodes the truck factor. (Mancuso 2014; Tornhill 2024)
- **Trusting confident memory over data** - false memories and biases mislead design decisions; keep a decision log and confirm with version-control evidence. (Tornhill 2024)
- **Scaling the wrong dimension** - throwing people at a late project (Brooks's Law) when the architecture/organization cannot absorb them, producing process loss and volatile, unmaintainable code. (Tornhill 2024)
- **God Classes as coordination bottlenecks** - code smells that force many developers into the same file and breed defects, unhappiness, and attrition. (Tornhill 2024)
- **Ignoring implicit/change coupling** because no explicit dependency is visible, leading to surprise breakages. (Tornhill 2024)

## Decision guidance
- **Should I tidy first?** If tidying makes the total (tidy + change) cheaper than the change alone, yes. If the area will change again soon and tidying is cheap now and proportional in size, tidy after. Otherwise add it to a Fun List and tidy later. Only "never" if behavior truly will not change again. Weigh option value against earning sooner.
- **How much caution for a change?** Gauge reversibility. Reversible structure change: act, do not over-review. Irreversible or propagating change: slow down, prototype, or first make it reversible (feature flags, reduce blast radius via tidying).
- **Where to spend improvement effort?** Run a hotspot analysis and start with the hottest, most complex modules; check their complexity trend; run change-coupling analysis to find hidden entanglement; run organizational analysis to find coordination bottlenecks and truck-factor risk. Act on the intersection of technical and social signals.
- **How to scale a team?** Watch output-per-developer vs. team size; if adding people lowers it, stop (Brooks's Law). Use Conway's Law deliberately: shape teams to match the desired architecture and align ownership with module boundaries.
- **How to behave professionally?** Own your learning, partner productively with clients, keep code well-crafted and changeable, and justify practices by the business value of shorter feedback loops.

## Source attribution
- "Beneficially relating elements," structure vs behavior, reversibility, coupling, Constantine's Equivalence, cohesion, time value/optionality, Options vs Cash Flows, the tidyings catalog, Separate Tidying, First/After/Later/Never: Beck 2023, Parts II–IV (ch. 1–33).
- Software craftsmanship definition and history, the Software Craftsmanship Manifesto and its four value pairs, career ownership and the professional/partnership ethos, XP technical practices and their business value, pragmatic refactoring and the Boy Scout rule: Mancuso 2014, ch. 2–7, 15.
- Hotspots (complexity x change frequency), offender-profiling analogy, defect/security correlations; complexity trends and Lehman's law; change (temporal) coupling and implicit dependencies; Brooks's Law, diffusion of responsibility, Conway's Law and Team Topologies, truck/bus factor and knowledge maps, code-quality's effect on morale and retention: Tornhill 2024, ch. 1–5, 8–9, 12–15.

## Recent developments (2024–2026)
- **Beck extends "empirical software design" toward AI-augmented work.** Beck (a pioneer of the AI-augmented development conversation) has been writing and speaking through 2024-2026 on what AI changes about software design and what stays the same, including "Vibe Coding: More Experiments, More Care" at O'Reilly's Coding with AI, arguing that AI raises, not lowers, the value of cheap reversible experiments and tidy structure. (kentbeck.com, 2025, https://kentbeck.com/; tidyfirst.substack.com, "Empirical Software Design Q/A", https://tidyfirst.substack.com/p/empirical-software-design-qa)
- **The "Empirical Software Design" series is expanding.** Tidy First? (2023) is positioned as the first of a series; the next installment focuses on how teams (not just individuals) practice design together while shipping features, with draft chapters released to subscribers through 2024-2025. (tidyfirst.substack.com, 2024-2025)
- **AI makes reversibility and small-batch tidying more valuable, not less.** With LLM agents generating large diffs, the structure-vs-behavior split and the First/After/Later/Never economics gain renewed relevance for keeping AI-assisted changes small, reviewable, and reversible; the optionality argument (options beat things under uncertainty) maps directly onto experimenting with AI output. (Beck talks/writing, 2025)
- **Behavioral code analysis (Tornhill) aligns with AI-readability concerns.** Hotspot, change-coupling, and knowledge-map analysis remain the standard data-driven way to prioritize refactoring; in 2025-2026 these signals are increasingly paired with the new worry that complex, deeply abstracted code degrades both human and LLM comprehension, strengthening the case for targeting hotspots first. (Your Code as a Crime Scene 2nd ed. methods, applied in current resilience/quality reviews)
- **Core ideas are stable.** Constantine's Equivalence (cost ≈ coupling), tidyings, the craftsmanship ethos, and forensic version-control analysis saw no reversal in this period; the shift is contextual (AI-assisted development) rather than a change to the underlying theory.
