# Shape Up and Betting

**Domain:** 03 - Product Discovery and Planning
**Primary sources:** Shape Up: Stop Running in Circles and Ship Work that Matters (Ryan Singer, 2019, Basecamp)

## Overview
Shape Up is Basecamp's product development method, refined over roughly fifteen years and documented by Ryan Singer in 2019. It is a deliberate alternative to Scrum, Kanban, and ad hoc project work. Instead of breaking work into two week sprints, grooming a backlog, and tracking velocity, Shape Up organizes work into three distinct activities done by different people on different tracks: shaping (defining the work at the right level of abstraction), betting (deciding what to fund for one cycle), and building (handing a whole project to a small autonomous team).

The method targets a specific risk: the risk of not shipping on time, of getting bogged down and never being free to do tomorrow what you want to do. It is not primarily about building the right thing (that is the domain of discovery and validation); it assumes you must first regain the basic ability to finish and ship. The central mechanics are the six week cycle, the fixed time / variable scope principle, and the circuit breaker. Together they create a system where teams reliably ship meaningful work and senior people get a "hands on the wheel" feeling about product direction.

## Core concepts

- **Shaping.** The pre-work of defining a project before it is bet on. Shaped work has three properties: it is **rough** (clearly unfinished, leaving room for the team), **solved** (the main elements connect and the approach is worked out), and **bounded** (it states what not to do and has a fixed appetite). Shaping is primarily interaction-design work done by a senior generalist or a small pair behind closed doors.
- **Appetite.** A time budget set before any design happens. "Estimates start with a design and end with a number; appetites start with a number and end with a design." Two standard sizes: **Small Batch** (one designer plus one or two programmers, one to two weeks) and **Big Batch** (the same team, a full six weeks).
- **Fixed time, variable scope.** The core principle. Time is fixed; scope flexes to fit. A deadline forces trade-offs and makes "good" relative to the constraint rather than to an unreachable ideal.
- **The pitch.** The written artifact that packages shaped work for the betting table. Five ingredients: Problem, Appetite, Solution, Rabbit holes, No-gos.
- **The bet.** A commitment of one six week cycle to one shaped project, given to one team, uninterrupted. A bet has a payout (something meaningful finished), is a real commitment (no pulling the team away), and has a capped downside (you can lose at most six weeks).
- **The circuit breaker.** If a project does not ship within the bet, it does NOT get an automatic extension. This caps runaway projects and forces the team to make hard scope trade-offs.
- **Scopes.** Integrated vertical slices of a project (front-end plus back-end) that can be finished independently. Scopes become the language of the project and the unit shown on hill charts. Discovered, not planned up front.
- **Hill chart.** A status visualization: every piece of work has an uphill "figuring it out" phase (unknowns, problem solving) and a downhill "execution" phase (certainty, knowing what to do). Scopes are plotted as dots on the hill.

## Frameworks & models

**The shaping process (four steps).**
1. **Set boundaries.** Set the appetite on a raw idea and narrow the problem. Respond to raw ideas with a soft "Interesting, maybe someday" rather than yes/no. Watch out for grab-bags (vague "redesigns" or "2.0" labels with no single driving problem). Replace "What could we build?" with "What's really going wrong?" Find the specific use case (the calendar request that was really "help me see free spaces").
2. **Find the elements.** Sketch a solution at the right level of abstraction using two techniques. **Breadboarding** draws Places, Affordances, and Connection lines in words only (borrowed from electrical engineering) to play out a flow without committing to visual design. **Fat marker sketches** use strokes so broad that detail is impossible, for ideas that are fundamentally about 2D layout. Wireframes are too concrete (they kill designer creativity and hide complexity); words alone are too abstract.
3. **Address risks and rabbit holes.** Slow down and stress-test the solution. Walk a use case in slow motion. Patch holes by dictating a solution at tricky spots, declaring things out of bounds, or cutting back. Present to technical experts late (ask "is X possible in six weeks?" not "is X possible?") to hunt for time bombs.
4. **Write the pitch.** Package it for the betting table.

**The betting table.** A short meeting (one to two hours) held during cool-down, attended by the highest decision-makers (at Basecamp: CEO, CTO, a senior programmer, the product strategist). No backlog grooming - just a few well-shaped pitches. Output is the cycle plan plus a kick-off message. Buy-in is from the very top so there is no second approval step and no later interference.

**Six week cycle plus two week cool-down.** Six weeks is long enough to finish something meaningful and short enough to feel the deadline from the start. Two-week sprints were rejected as too short and too costly in planning overhead. Cool-down is unscheduled time for fixing bugs, exploring, and planning the next bets.

**New-product modes (place your bets).** R&D mode (spike, do not expect to ship; senior people build because you cannot delegate what you do not yet understand), Production mode (deliberate shaping, "shipping" means merging to main, parallel teams possible), Cleanup mode (no shaping, free-for-all before launch, leadership makes final-cut decisions).

**Scope mapping shapes.** Layer cakes (thin even back-end under UI - integrate design and code in one scope), Icebergs (much more back-end than UI, or the reverse - factor UI out as its own scope), Chowder (a short list of loose tasks; if longer than three to five items, a scope is hiding), nice-to-haves marked with ~.

## Patterns

- **Fixed time, variable scope** - intent: ship reliably by making scope the adjustable variable and time the constant. When to use: any project where shipping on time matters more than feature completeness. Trade-offs: requires discipline to cut scope and a culture that compares "down to baseline" not "up to the ideal."
- **Appetite over estimate** - intent: constrain design by deciding up front how much the idea is worth. When to use: shaping new features. Trade-offs: needs senior judgment; not a substitute for estimation in fixed-bid or contractual contexts.
- **Circuit breaker** - intent: cap downside risk by refusing automatic extensions. When to use: time-boxed cycles with capped bets. Trade-offs: a genuinely valuable project can miss a cycle and must be reshaped and re-bet; true crises still need an escape hatch.
- **Hill chart** - intent: show status as known-vs-unknown rather than percent-complete, surfacing stuck work without anyone having to say "I don't know." When to use: tracking in-cycle progress. Trade-offs: requires honest self-reporting; dots that backslide reveal "head work vs hand work."
- **Breadboarding** - intent: design flows at the topology level (what connects to what) without visual detail. When to use: early shaping of interaction flows. Trade-offs: "you had to be there" quality; needs translation for the pitch.
- **Get one piece done (vertical slice first)** - intent: integrate one core, small, novel slice end-to-end in week one to build momentum and de-risk. When to use: project kick-off. Trade-offs: feels like radio silence early; managers must respect the orientation phase.
- **No backlog / decentralized lists** - intent: avoid an ever-growing pile of obligations by letting each function track ideas independently and re-lobby each cycle. When to use: replacing a grooming-heavy backlog. Trade-offs: relies on "important ideas come back"; can feel like losing institutional memory.

## Principles & best practices
- Always present a problem and a solution together; a problem with no solution is unshaped work, a solution with no problem has no test of fitness.
- Assign projects, not tasks. Hand a whole, intact pitch to a small integrated team and let them define their own tasks. "Done means deployed" within the cycle.
- Distinguish imagined tasks (assumed up front) from discovered tasks (found by doing real work). To-do lists grow as you make progress; this is normal.
- Start in the middle: build the core, small, novel piece first; stub everything else (hard-coded auth, fake data) to get there.
- Sequence by risk: push the scariest, most uncertain scopes uphill first (inverted pyramid). Leave routine work (email templates) for the final week.
- Compare to baseline when deciding when to stop. The bar is "better than what customers have now," not perfection. Cutting scope is not lowering quality.
- Bugs are not automatically special. Handle them in cool-down, at the betting table, or in a once-a-year "bug smash" cycle.
- Keep the slate clean: bet only one cycle ahead so you stay free to respond to the unexpected.

## Anti-patterns & pitfalls
- **Grab-bags and "2.0" projects** - undefined scope with no single driving problem; impossible to know when "done."
- **Wireframes too early** - over-specifies design, hides implementation complexity, and biases everyone downstream.
- **Conveyor-belt thinking** - assuming every shaped idea must be built. Shaping adds value to a raw idea without any commitment to act.
- **Treating "I don't know" as failure** - drives uncertainty underground and lets risk accumulate to the end of the cycle.
- **Backsliding hill dots** - usually means uphill work was done "in the head" (a theory) not "with the hands" (validated by building).
- **Junk-drawer scopes** ("front-end," "bugs") - signal not enough integration; you can never mark them done.
- **Interrupting a bet** - pulling someone away for "just a day" loses the momentum, not just the day.

## Decision guidance
- Use Shape Up when the main pain is failing to ship, projects dragging on, and senior people having no time to think strategically. It assumes you can already roughly judge what is worth building.
- Choose appetite (Small Batch vs Big Batch) by how much the idea is worth, not by how big the solution seems. If it cannot conceivably fit six weeks, narrow the problem or break off a meaningful sub-project.
- At the betting table reason through five questions: Does the problem matter? Is the appetite right? Is the solution attractive? Is this the right time? Are the right people available?
- For multi-cycle work, still bet only one cycle: shape a specific six-week target that is fully built and working at the end, then re-bet.
- Adapt to company size: the "basic truths" (appetite, fixed time/variable scope, shaping, scopes, hill) are universal; the specific practices (formal betting table, dedicated shaping track) scale up as the team grows. Small teams can wing the ceremonies.

## Source attribution
- Shaping properties, breadboarding, fat marker sketches, rabbit holes, the pitch: Singer 2019, Part 1 (chapters 2–6).
- Bets vs backlogs, six week cycle, cool-down, betting table, circuit breaker, bug handling, new-product modes: Singer 2019, Part 2 (chapters 7–9).
- Hand over responsibility, get one piece done, scope mapping, hill charts, deciding when to stop, scope hammering: Singer 2019, Part 3 (chapters 10–15).
- Adjusting to company size and implementation guidance: Singer 2019, Appendices.

## Recent developments (2024–2026)

- **Shape Up remains a stable, widely-referenced alternative to Scrum.** The method is unchanged since the 2019 book; 2024-2026 writing positions it as a standing alternative to Scrum and Kanban, valued for eliminating backlog grooming, estimation sessions, and daily standups in favor of fixed-time/variable-scope bets and team autonomy. The book itself remains the canonical source. (Basecamp, shapeup.cc; Marmelab, "Shape Up: Should You Change Your Agile Methodology?," 2024; IdeaPlan, "Shape Up vs Scrum," 2026)

- **Hybridization is the dominant real-world pattern.** Practitioner reports consistently note that few teams adopt Shape Up purely; the common pattern is borrowing its principles into existing agile setups, fixed-time thinking and appetites instead of story-point estimation, shaping work to fit a sprint, and betting at the cycle boundary, while retaining Scrum-style cross-team coordination and retrospectives that Shape Up deliberately omits. (Bob Stanke, "Shape Up vs Scrum"; Graph AI, 2025; Selleo, 2025)

- **AI is entering shaping and betting as an assistant.** Basecamp's current materials reference shaping "with your team and your AI agent," and community "Shape Up" agent skills have appeared, using AI to help draft pitches, stress-test rabbit holes, and break a shaped project into scopes. This augments the senior generalist's shaping work rather than changing the method's mechanics (appetite, fixed time/variable scope, circuit breaker, hill charts). (Basecamp Shape Up materials, 2025; agentskills.so, 2025)

- **Acknowledged gaps reinforced.** 2024-2026 comparisons restate the known trade-offs: Shape Up provides little built-in cross-team coordination, forecasting, or regular retrospective cadence, so larger or highly interdependent organizations tend to graft those back on. This matches the book's own "adjust to company size" guidance. (Bob Stanke, 2025; IdeaPlan, 2026)

- **Core mechanics stable.** Shaping, appetites, the pitch, the betting table, the six-week cycle plus cool-down, the circuit breaker, scopes, and hill charts are unchanged and still taught as written. The 2024-2026 developments are continued steady adoption, pervasive hybridization with agile, and AI assistance in shaping, not a revision of the method.
