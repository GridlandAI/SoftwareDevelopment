# Lean Product and MVP

**Domain:** 03 - Product Discovery and Planning
**Primary sources:** The Lean Product Playbook (Dan Olsen, 2015)

## Overview
The Lean Product Process is a structured, hypothesis-driven method for achieving product-market fit before you waste engineering effort building the wrong thing. It belongs at the front of the SDLC: it decides who you build for, what unmet need you address, why your product wins, what the smallest valuable version is, and how you validate all of that with real customers. Dan Olsen frames the whole effort around a single organizing model, the Product-Market Fit Pyramid, and a six-step process that walks up that pyramid from the bottom (market) to the top (user experience).

The core mindset shift is separating problem space from solution space, and iterating between them. Most product failures come from jumping straight into "how" (a specific implementation) without rigor about "what" (the customer need) and "who" (the target customer). Lean Product is the discipline that forces those assumptions to become explicit, testable hypotheses that you revise as evidence arrives.

## Core concepts

**Product-market fit (PMF).** Olsen uses the core definition (consistent with Marc Andreessen): you have built a product that creates significant customer value, meaning it meets real customer needs better than the alternatives. He deliberately excludes monetization and customer-acquisition economics from the definition; you must create value before you can capture it. Creating value (PMF) and capturing value (business model, pricing, acquisition) are distinct.

**Problem space vs. solution space.** Problem space is where all customer needs live: pain points, desires, jobs to be done, user stories, benefits. Solution space is any product or representation of a product (mockups, wireframes, prototypes, live code). The "what" is problem space; the "how" is solution space. The Space Pen story illustrates the danger of jumping into solution space prematurely (a costly zero-gravity pen vs. a pencil vs. "a way to record notes in zero gravity"). Markets are defined by problems, not solutions (TurboTax's biggest competitor was pen and paper, not TaxCut), which is why market disruptions happen when a new solution better meets the same enduring need.

**Outside-in vs. inside-out development.** Inside-out starts with what employees think is good to build and never tests it with customers. Outside-in starts with understanding the customer's problem space, then forms and tests hypotheses on early solution-space artifacts. "Get out of the building" (Steve Blank). Customers will not invent your breakthrough, but understanding their needs and reactions is essential. Use solution-space artifacts to discover problem-space needs, because customers give far better feedback reacting to something concrete than discussing abstract needs.

**Minimum Viable Product (MVP).** The minimum amount of functionality your target customer considers viable, meaning it provides enough value. "Minimum" is not an excuse for partial, buggy, or ugly. An MVP is limited in functional scope but should still be reliable, usable, and delightful (the "complete MVP" pyramid: functional → reliable → usable → delightful). Olsen distinguishes an MVP (an actual product) from MVP tests (methods to test the hypotheses behind the MVP), which dissolves the debate over "is a landing page an MVP?"

## Frameworks & models

**The Product-Market Fit Pyramid (5 layers).** Bottom-up:
1. Target customer (market)
2. Underserved needs (market)
3. Value proposition (product)
4. Feature set (product)
5. UX (product)

The bottom two layers are the market; the top three are your product. PMF is how well the top three satisfy the bottom two. Each layer depends on the one below it. The interface between problem space and solution space sits between value proposition (problem space, the layer you control most) and feature set (solution space).

**The Lean Product Process (6 steps).** A chapter-per-step process up the pyramid:
1. Determine your target customer
2. Identify underserved customer needs
3. Define your value proposition
4. Specify your MVP feature set
5. Create your MVP prototype
6. Test your MVP with customers

Not all six are required every time; once target customer, needs, and value prop are validated you loop mainly through specify-build-test for new features ("minimum viable feature").

**Market segmentation (Step 1).** Segment by demographic (age, gender, income; firmographics/SIC/NAICS for B2B), psychographic (attitudes, values, interests), behavioral (actions and frequency), and needs-based (groups sharing distinct needs, e.g., Dropcam's baby-monitor, security, pet, and business segments). Distinguish users from buyers (and economic buyers from blockers) in B2B. Use the Technology Adoption Life Cycle (innovators, early adopters, early majority, late majority, laggards; "Crossing the Chasm," Geoffrey Moore) to know which segment you are targeting and the chasm between early adopters and the early majority.

**Personas (Step 1).** Single-page archetypes (Alan Cooper, Goal-Directed Design) capturing demographic, psychographic, behavioral, and needs attributes plus name, photo, a defining quote, goals, motivations, frustrations, expertise level, usage context, and adoption-cycle segment. Build from interviews and surveys; never populate a persona with averages ("no one has 2.3 children"). Treat as a living first draft; do not stop talking to customers once personas exist.

**Customer needs frameworks (Step 2).**
- *Benefit ladders ("laddering").* Keep asking "why is that important?" to climb from detailed benefits to higher-level ones (TurboTax: feel confident, save time, save money). Related to Toyota's / Ries's Five Whys.
- *Hierarchies of needs.* Maslow-style dependencies: a higher need does not matter until lower ones are met. Olsen's Hierarchy of Web User Needs (bottom to top): availability, performance/speed, quality (works correctly), feature set, UX. "Dip down" to fix lower levels when a new feature degrades them.
- *Importance vs. Satisfaction framework.* Plot needs by importance (vertical, unipolar 0-100%) and current satisfaction (horizontal, bipolar). The upper-left quadrant (high importance, low satisfaction) is the underserved sweet spot and the best place to create value (Uber vs. taxis). Use 5-point unipolar scales for importance, 7-point bipolar for satisfaction.
- *Gap analysis.* Gap = Importance − Satisfaction. Simple but treats all equal-size gaps alike.
- *Outcome-Driven Innovation / Jobs to Be Done (Ulwick; Christensen).* Opportunity score = Importance + max(Importance − Satisfaction, 0). Adds importance as a tiebreaker so high-importance gaps rank higher. Customers "hire" products to get a job done; aim for >15 opportunity score, avoid <10.
- *Visualizing customer value.* Customer value delivered = Importance × Satisfaction (area of the rectangle). Opportunity = Importance × (1 − Satisfaction). Value created by an improvement = Importance × (Sat_after − Sat_before).
- *Kano model.* Three need types: must-haves (table stakes; absence causes dissatisfaction, presence does not delight), performance benefits (more is better; satisfaction scales linearly), and delighters (unexpected; presence delights, absence causes no dissatisfaction). Needs migrate over time: delighters become performance features become must-haves.

"Doing quant on qual" and "a sample size of zero is okay": you can use these frameworks to formulate and sharpen hypotheses before talking to a single customer, and meaningful patterns can emerge from 25 interviews without statistical significance.

**Value proposition template (Step 3).** A grid: rows = benefits grouped into must-haves / performance benefits / delighters; columns = each competitor (or alternative) and your product. Score must-haves Yes/No, performance benefits High/Medium/Low (or numeric), and list delighters individually. Your product column, with intended scores and bolded key differentiators, is your value proposition. "Strategy means saying no." Add "now" and "later" columns to skate to where the puck will be (anticipate trends; the Flip camera lost to smartphones).

**MVP feature set (Step 4).** Brainstorm feature ideas per benefit (divergent then convergent). Write features as user stories ("As a [user], I want to [do something], so that I can [benefit]") meeting INVEST (Independent, Negotiable, Valuable, Estimable, Small, Testable). "Chunk" features into smaller pieces (small batch sizes give faster feedback, less rework). Prioritize by ROI = customer value created / development effort (developer-weeks), or an approximate 3×3 value-vs-effort grid. Select the minimum set: all must-haves, enough of your differentiating performance benefit, and your top delighter. Push the rest into a rough v1.1 / v1.2 roadmap, but do not plan far ahead because customer feedback will change things.

**MVP prototype types (Step 5) - the 2×2 matrix of MVP tests.** Categorized by product vs. marketing and qualitative vs. quantitative:
- *Qualitative marketing:* show marketing materials (landing page copy, video, ads); the five-second test.
- *Quantitative marketing:* landing page / smoke test (measure conversion rate; Buffer), explainer video (Dropbox), ad campaigns (test taglines), marketing A/B and multivariate tests, crowdfunding (Pebble, Oculus) to validate willingness to pay.
- *Qualitative product:* design artifacts ranked by fidelity and interactivity - hand sketches (internal only), wireframes (low/medium fidelity, clickable), mockups/comps (high fidelity, clickable via InVision), interactive prototypes; plus manual-hack MVPs: the Concierge MVP (openly manual, e.g., Airbnb professional photography) and the Wizard of Oz MVP (manual hidden behind a curtain that looks like the real product); plus live product testing (moderated or unmoderated).
- *Quantitative product:* fake door / 404 page test (Zynga five-word pitches), product analytics, product A/B tests.

**MVP testing (Step 6).** Test qualitatively one customer at a time (avoid focus-group groupthink), in waves of 5-8, ideally in person (richer cues than remote-moderated or unmoderated). Recruit with a screener tied to your persona; schedule users on a recurring cadence to avoid the scheduling trap ("Ramen user testing," "Starbucks user testing"). Use the think-aloud protocol, ask open questions ("why/how/what"), echo back observations, answer questions with questions, and never ask leading questions. Track results across waves in a table (percentage of users hitting each issue), prioritize fixes by frequency and by pyramid layer.

**Hypothesize-Design-Test-Learn loop (Olsen's refinement of Build-Measure-Learn).** Olsen reframes Eric Ries's Build-Measure-Learn loop because (a) "build" overstates: you only need something to test (design artifacts count); (b) "measure" overstates: qualitative observation counts, not just A/B data; (c) "learn" is really two steps, learn then re-hypothesize, and the whole loop actually starts with a hypothesis. The loop moves problem space → solution space (design) → test with customers → validated learning → revised hypotheses. Address issues at the lowest pyramid layer first because lower-layer changes ripple upward.

## Patterns

**Product-Market Fit Pyramid walk-up** - Intent: build a product from validated foundations. When to use: new product, redesign, or major feature where target customer and value prop are uncertain. Trade-offs: front-loads thinking and customer research; slower to first code but far less rework.

**Concierge MVP** - Intent: validate what a product/service should do by manually serving a few early customers. When to use: service-heavy offerings with high customer interaction, before automating. Trade-offs: not scalable; high per-customer effort; but maximal learning and minimal build risk (Airbnb).

**Wizard of Oz MVP** - Intent: test a live-looking product whose backend is performed by humans. When to use: when you want real usage signal before building automation. Trade-offs: deceptive by design (customer does not know), labor-intensive, only viable short-term.

**Smoke test / landing page** - Intent: quantify demand and willingness to act before building. When to use: validating value prop and messaging at scale cheaply. Trade-offs: measures intent (clicks, emails) not real value delivery; depends on copy and visual design quality (Buffer, Dropbox).

**Fake door / 404 test** - Intent: gauge demand for a feature before building it. When to use: existing product with traffic, evaluating a new feature. Trade-offs: can frustrate users if overused; only measures click-intent, not satisfaction.

**Feature chunking + ROI prioritization** - Intent: ship the smallest valuable slice first. When to use: scoping any MVP or release. Trade-offs: requires effort estimates of uncertain accuracy; the value is in relative ranking, not absolute numbers.

## Principles & best practices
- Separate and alternate between problem space and solution space; "customers care about their problems, not your solution."
- Make hypotheses explicit so you can test them; if you do not state assumptions, you are making them implicitly.
- Pursue upper-left-quadrant opportunities: high importance, low satisfaction.
- Strategy means saying no; focus the value proposition.
- Work in small batch sizes for faster feedback and less rework.
- A "complete" MVP is reliable, usable, and delightful, not just minimally functional.
- Test qualitatively before quantitatively; qualitative tells you "why," quantitative tells you "what" and "how many."
- One customer at a time, in waves of 5-8, think-aloud, open questions, no leading questions.
- Address the lowest broken pyramid layer first.
- Quant on qual is legitimate; a sample size of zero (forming hypotheses) is a valid starting point.

## Anti-patterns & pitfalls
- Inside-out development: building what the team likes without customer validation.
- Treating "minimum" in MVP as license for buggy, ugly, or non-viable products.
- Jumping to solution space before defining the problem space (Space Pen).
- Defining your market by solutions rather than by customer needs (missing pen-and-paper as a competitor).
- Personas built on averages or speculation, then never revisited.
- Focus groups and groupthink in user testing; leading and closed questions from the moderator.
- The scheduling trap: trying to recruit testers only once an artifact is "ready," which kills testing cadence.
- "Me too" products from skipping the value-proposition exercise.
- Over-planning a roadmap before MVP feedback arrives.
- Relying solely on quantitative testing and never learning the "why."

## Decision guidance
- Choose an MVP test type by what you most need to learn: messaging/demand → marketing tests; product value → product tests; "why" → qualitative; "how many" → quantitative.
- Early in discovery, prefer moderated, in-person, qualitative tests; later, add unmoderated and quantitative tests for speed and scale.
- Prioritize needs by opportunity (high importance × low satisfaction), then features by ROI, preferring smaller-scope items at equal ROI.
- When feedback invalidates a hypothesis, locate it on the pyramid; fix lower layers before higher ones, and expect lower-layer changes to ripple up.
- For services with heavy interaction, prefer a concierge MVP; for products where automation perception matters, consider Wizard of Oz; for pure demand validation, a smoke test or fake door.
- Use the value-proposition grid to decide explicitly what NOT to build.

## Source attribution
- Product-Market Fit Pyramid, the six-step Lean Product Process, problem vs. solution space, the Space Pen and TurboTax examples: The Lean Product Playbook, Ch. 1-2 (Olsen, 2015).
- Segmentation, users vs. buyers, technology adoption life cycle, personas: Ch. 3.
- Needs terminology, benefit ladders, needs hierarchies, Importance vs. Satisfaction, gap analysis, Jobs to Be Done / Ulwick, visualizing customer value, Kano model: Ch. 4.
- Value proposition template and "strategy means saying no": Ch. 5.
- User stories, INVEST, feature chunking, small batch sizes, ROI prioritization, MVP candidate selection: Ch. 6.
- MVP vs. MVP tests, the 2×2 test matrix, fidelity/interactivity of artifacts, Concierge and Wizard of Oz MVPs, Buffer/Dropbox/Airbnb/Zynga examples: Ch. 7.
- UX usability and Olsen's Law of Usability: Ch. 8.
- Qualitative user-testing logistics (waves, screeners, moderation, open questions): Ch. 9.
- Build-Measure-Learn and the Hypothesize-Design-Test-Learn loop, iterate and pivot: Ch. 10.

## Recent developments (2024–2026)

- **"Vibe coding" collapsed the cost of an MVP.** AI-native tools (Cursor, Replit, Lovable, v0, Bolt and similar) let teams generate working prototypes in hours and ship a functional MVP in days rather than months. Reports cite cost reductions of 50-90% and timelines compressed from 6-12 months to 1-4 weeks. Y Combinator reported that about 25% of its Winter 2025 batch had codebases that were ~95% AI-generated. This sharpens, rather than overturns, Olsen's MVP-vs-MVP-test distinction: building an artifact is now so cheap that the discipline shifts almost entirely to deciding what is worth building. (Y Combinator, 2025; Codelevate, 2025; Aimsys, 2025)

- **The recommended pattern: prototype to validate, then rebuild for fit.** A consistent 2025 practitioner stance is to use vibe coding strategically for rapid prototyping, validation, and speed to market, then re-engineer properly once product-market fit appears. This maps directly to Olsen's loop (hypothesize, design a cheap artifact, test, learn) and to the warning that "minimum" is not license for a fragile product once you commit to scale. (Codelevate, 2025; Lumi Studio, 2025)

- **The failure mode is unchanged: building the wrong thing.** Even as build cost falls, the 2025 commentary reiterates that startups fail not because they build slowly but because they build the wrong thing, which keeps the problem-space-before-solution-space discipline central. Cheaper building raises, not lowers, the value of the Importance-vs-Satisfaction and value-proposition work that decides what to build. (virbits, 2025; Indapoint, 2025)

- **AI compresses the test loop itself.** Beyond code generation, teams use AI to draft landing-page copy and variants, synthesize qualitative test sessions, and run faster iteration cycles (ideate, prototype, test with users, synthesize, refine) sometimes within a single day, extending the book's smoke-test and qualitative-testing methods with faster turnaround. (Nucamp, 2025; Aimsys, 2025)

- **Core model stable.** The Product-Market Fit Pyramid, the six-step Lean Product Process, problem vs. solution space, Kano, Importance-vs-Satisfaction, and the MVP-test taxonomy (concierge, Wizard of Oz, smoke test, fake door) remain the reference framework. The 2024-2026 change is the radically lower cost and time of producing solution-space artifacts and synthesizing test results, not a change to the underlying method.
