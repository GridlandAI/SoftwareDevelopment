# Estimation and Planning

**Domain:** 03 - Product Discovery and Planning
**Primary sources:** Software Estimation Without Guessing: Effective Planning in an Imperfect World (George Dinwiddie, 2020, Pragmatic Bookshelf)

## Overview
Dinwiddie reframes estimation away from "getting the closest number" toward "meeting the actual business need." An estimate is not a calculation or a fact; it is an approximate judgment with a limited shelf life. The book's organizing metaphor is *headlights*: estimates let you see far enough ahead to steer, slow down in fog, and avoid painting yourself into a corner - the value is in better decisions, not in precision for its own sake.

The most important move is to ask *what question the estimate answers* and *who needs it and why*, because the required accuracy, precision, and acceptable direction of error all depend on the decision at stake. The book covers comparison-based estimation, decomposition, progress checking, model-based estimation, milestones, what to do when estimates and actuals differ, and the human conflicts that surround estimation. It is sympathetic to but careful with #NoEstimates ideas (e.g., counting stories instead of pointing them) and biased toward iterative work steered toward outcomes.

## Core concepts

- **Estimate vs forecast vs guess.** All near-synonyms, but an estimate is an educated judgment based on relevant experience, distinct from a pure guess. "The abbreviation of estimate is guess" - a joke that warns against false confidence.
- **Accuracy vs precision.** Accuracy is correctness (does it match actuals); precision is how tightly the estimate is stated. "About a year" finishing in 390 days is accurate at ±1 month but inaccurate if interpreted as "1 year or less." Over-precise statements ("365 days") imply false confidence. Be wary of more than two significant digits.
- **Direction of error and confidence level.** Some decisions want errors to cancel out (50% confidence, e.g., iteration planning); others want errors piled on the safe side (high confidence, e.g., a legal deadline). Match the estimating method to the desired error distribution.
- **The three reasons to estimate (by importance).** (1) A decision depends on it now (start/continue/cancel). (2) A decision *might* depend on it later if reality diverges. (3) Reassuring yourself things are OK - the most suspect, and where blaming begins.
- **Story points, planning poker, counting stories.** Story Points (unitless, from the C3/XP project) let you calibrate estimates to what actually happens. Planning Poker (James Grenning) was invented to unstick a meeting, popularized by Mike Cohn. Dinwiddie reports that *counting* stories often has as much short-run predictive power as pointing them, and far less cost.
- **Walking skeleton.** The simplest end-to-end slice connecting input to output; the natural first piece to build and estimate against.

## Frameworks & models

**Comparison-based estimation.** Compare the unknown work to known past work. Ask: what past work was similar? Same size, bigger, or smaller? How does it differ? How many of those would make up this? Apply **multiplicative adjustments** (this is ~2× as complex) and **additive adjustments** (plus two months to learn a new stack), but resist orders of magnitude - people cannot reliably judge "100× vs 1000×." Augment memory with recorded data, but beware inaccurate memories (remembering the estimate not the actual) and inaccurate records (unrecorded overtime, mis-charged time, omitted meetings/data migration).

**Aspects to compare (checklist).** System (quantitative counts: workflows, screens, stored items, integrations; qualitative: complexity, novelty; quality: maintainability expectations). System context (constraints, the "-ilities" - scalability, availability, reliability, performance; security and auditability; usability/accessibility/i18n; priority of when these matter). Development context (familiarity with domain and tech; team relationships and the customer's clarity/decisiveness - "bring me a rock"). Neglecting an aspect that *differs* hurts the estimate; neglecting one that is roughly the same does not.

**Decomposition for estimation.** Slice by phase, by implementation, or by functionality (user stories preferred). Trade-offs: a **large number of small parts** gives detail but is itself costly and locks in a naive design; a **small number of large parts** is faster but coarser. Use **affinity estimation** to sort items by relative size; **multi-level decomposition** with *different scales for different granularities* (1,2,3,5,8 for stories; T-shirt sizes or animals - rat/cat/dog/pig/cow - for epics) so no one does false arithmetic like "two cats = one dog."

**Decomposition gotchas.** Consistent size bias (developers are optimists - errors add up rather than cancel); overlooking the space between pieces (setup, reading code, verifying - "never estimate a task under 30 minutes"); overlooking small pieces (the work itself prompts related tasks you missed); too many pieces (a 1000-story backlog is a Work Breakdown Structure in disguise, built when you know least); the Coastline Paradox / fractal estimation (the finer you look, the larger and more uncertain the total); losing focus; imagining pieces that do not match the eventual implementation. Cognitive biases to watch: Sunk Cost, Anchoring, Confirmation, Precision, Numeracy.

**Checking progress.** Do not trust percent-complete or effort/Earned-Value as proxies for value. Measure *functionality* - thin vertical slices of working, tested behavior (Ron Jeffries' Running Tested Features). Visualize with a **BurnUp Chart**: time on X, current progress and current goal on Y, so a moving goal line is visible and progress can be projected. Beware Goodhart's Law (a measure that becomes a target stops being a good measure), Parkinson's Law, and the side effects of pushing for raw speed (deferred quality, burnout, cut safety margins, hidden reality).

**Model-based estimation.** Linear models (size × rate), parametric models, stochastic (Monte-Carlo style) models, and comparison-model hybrids. Models let you recalculate whenever you want - useful for repeated progress checks - but building a trustworthy model is costly; comparison is the cheaper default.

**Estimating milestones and planning for incorrect predictions.** Set "danger bearings" - prior expectations that warn you early when you are off course. Deliberately create extra predictions so the inaccurate ones become information. When estimates and actuals differ: salvage the situation, then mine the disappointment for learning (which assumption was wrong?), and re-estimate frequently if the answer still matters.

## Patterns
- **Estimate to the question** - intent: produce an estimate fit for the specific decision. When to use: any estimate request. Trade-offs: requires backtracking up the request chain to learn the real "why" (ask *what* questions, not *why* questions, to avoid defensiveness).
- **Walking skeleton first** - intent: build/estimate the thinnest end-to-end slice to establish a real data point. When to use: starting a new project or component. Trade-offs: requires stubbing; not a full plan.
- **Counting over pointing** - intent: forecast short-run throughput cheaply by counting similarly-sized stories. When to use: iteration/release forecasting where stories are roughly uniform. Trade-offs: needs comparable story sizes; weaker for long-range or heterogeneous work.
- **Budget-and-validate the unknown** - intent: set a spending limit on infeasible-seeming work, validate it early, replan at the limit. When to use: feasibility questions ("can we make this work?"). Trade-offs: requires the discipline to stop at the budget and resist sunk cost.
- **Affinity / relative sizing with separate scales** - intent: size many items fast without false arithmetic across granularities. When to use: backlog or epic-level estimation. Trade-offs: relative not absolute; must calibrate to actuals.
- **BurnUp chart** - intent: show progress against a (possibly moving) goal as a better Earned Value tool. When to use: tracking and governance. Trade-offs: depends on a Work Breakdown Structure; slice sizing affects apparent speed.

## Principles & best practices
- Numbers are not the goal; successful outcomes are. Use estimates as headlights, not contracts.
- Treat the first approximation as good enough; ignore second-order effects until you need them.
- Constrain error in one direction when there is real danger; let errors cancel when there is not.
- Re-estimate frequently; estimates have a short shelf life, especially in changing contexts.
- Keep durable teams and bring work to them; this gives a track record that makes estimation easier and avoids ramp-up.
- Work on one thing at a time; multitasking destroys the ability to know how much effort actually went into a project.
- Separate value estimation from cost estimation, but make sure they share assumptions (otherwise a "kitchen sink" cost can torpedo a project whose value depended on a smaller scope).
- For Cost-of-Delay prioritization use CD3 (Cost of Delay Divided by Duration) / Weighted Shortest Job First, but remember Cost of Delay is itself an estimate.

## Anti-patterns & pitfalls
- **One estimate for all needs.** A single number cannot serve "is it worth starting," "what should we budget," and "reassure me" simultaneously.
- **Precision theater.** Reporting decimals or three significant digits to look trustworthy when the inputs are vague.
- **Pre-decomposing into a giant backlog.** Maximum planning when knowledge is minimum; the backlog grows anyway and items get quietly deleted (sunk cost makes this hard).
- **Story Points / Earned Value as progress.** Effort and difficulty are not value; "90% done, 10% left that takes 90% of the time."
- **Underbid and make it up in change orders.** A real but shady contracting pattern Dinwiddie names as a warning.
- **Feature parity in rewrites.** Forcing the new system to match every old feature inflates the estimate; resist it.

## Decision guidance
- Start by identifying the question, the decider, the needed accuracy/precision, and the desired error direction. Without these you cannot pick a method.
- Default to comparison-based estimation for new work; move to model-based only when you will estimate repeatedly and a calibrated model pays off.
- For iteration planning, estimate at ~50% confidence (errors cancel); for hard external deadlines, estimate conservatively at high confidence.
- For feasibility, identify what you *cannot* estimate and budget it; validate the scariest unknowns first.
- For prioritization across projects, use Cost of Delay / CD3 / WSJF, but treat the inputs as estimates and apply judgment over the score.
- When an estimate proves wrong, do not blame - salvage, learn which assumption failed, and re-estimate.

## Source attribution
- Headlights, needs-based estimation, accuracy/precision, confidence levels, intuitive/sensing/imposed projects, ROI/cashflow/capacity cases, Cost of Delay/CD3: Dinwiddie 2020, ch. 1.
- Comparison to past experience, multiplicative/additive adjustments, memory vs recorded data, aspects-to-compare checklist: ch. 2.
- Decomposition methods, affinity estimation, separate scales, decomposition gotchas, cognitive biases, walking skeleton: ch. 3.
- Checking progress, measuring functionality, BurnUp charts, Earned Value critique, Goodhart/Parkinson, governance: ch. 4.
- Linear/parametric/stochastic/hybrid models: ch. 5; milestones and danger bearings: ch. 6; estimates vs actuals: ch. 7; planning for incorrect predictions: ch. 8; people conflicts: ch. 9.
- Story Points / Planning Poker / counting-stories history: Dinwiddie 2020, Introduction.

## Recent developments (2024–2026)

- **Probabilistic forecasting (Monte Carlo) has gone mainstream.** 2024-2025 practitioner writing increasingly frames the choice as "stop estimating, start forecasting": instead of point estimates, run thousands of simulations over historical throughput to produce statements like "70% chance of finishing by Q3, 90% by Q4." This is the practical, tooling-supported maturation of Dinwiddie's stochastic/model-based estimation and his emphasis on confidence levels and direction of error. (InfoQ, "NoEstimates Project Planning Using Monte Carlo Simulation"; Agile Alliance, "Estimation and Forecasting," 2024-2025; Devoteam, 2025)

- **Throughput/story-count forecasting reinforced over story points.** The 2024-2025 consensus echoes Dinwiddie directly: counting stories (throughput) often forecasts as accurately as pointing them, at far lower cost, and #NoEstimates does not preclude planning, it just shifts from effort-points to flow-based forecasting. Monte Carlo built on per-sprint throughput is the common implementation. (InfoQ; Agile Alliance, 2025; montecarloestimation.com)

- **#NoEstimates reframed as honest forecasting, not no-planning.** Current commentary settles the debate the way the book does: the goal is honest, decision-serving forecasts rather than the elimination of planning, and point estimates are simply a weaker tool than probabilistic forecasts for many client-facing contexts. (Xcapit, 2025; PM Today, 2025)

- **AI-assisted estimation is emerging but unproven.** Tools now attempt to suggest estimates or forecasts from historical ticket data and project context, and AI assists with task breakdown and risk surfacing in agile tooling. The cautions in the book apply with force: anchoring, precision theater, and treating a generated number as a fact are all amplified when an AI produces a confident-looking estimate, so AI output should be treated as one input to judgment, not an answer. (agile tooling landscape, 2024-2025)

- **Core principles stable.** Estimate-to-the-question, accuracy vs. precision, comparison-based estimation, decomposition gotchas, the walking skeleton, BurnUp charts, and Cost of Delay / CD3 remain sound and unchanged. The 2024-2026 shift is the move toward probabilistic, flow-based forecasting (a tooling and framing change Dinwiddie's model-based chapter anticipates) rather than any new estimation theory.
