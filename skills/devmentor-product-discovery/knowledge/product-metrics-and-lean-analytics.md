# Product Metrics and Lean Analytics

**Domain:** 03 - Product Discovery and Planning
**Primary sources:** Lean Analytics (Alistair Croll and Benjamin Yoskovitz, 2013)

## Overview
Lean Analytics applies data discipline to the Lean Startup loop: it tells you which metric to focus on, at what stage, for what kind of business, and what "normal" looks like so you know when you have won and can move on. Its central thesis: by knowing your business model and your current stage, you can pick the One Metric That Matters (OMTM) right now, draw a line in the sand for it, run experiments to move it, and avoid the deadly trap of premature growth. This sits across discovery, planning, and operations in the SDLC because it governs how product decisions are validated with evidence rather than vanity.

The book reframes "data-driven" around the second word, driven: a metric is only worth tracking if it changes what you do. It pulls together prior frameworks (Pirate Metrics, Engines of Growth, Lean Canvas, Startup Growth Pyramid, the Long Funnel) and proposes its own Lean Analytics Stages with gating metrics, then provides industry benchmarks ("lines in the sand") so teams can judge whether a number is good enough.

## Core concepts

**What makes a good metric.** A good metric is (1) comparative (across time, segments, or competitors), (2) understandable (people can remember and discuss it), (3) a ratio or rate (easier to act on, inherently comparative, good for expressing tensions), and (4) behavior-changing (it makes you do something different). Metrics come in pairs behind a fundamental metric (e.g., conversion rate + time-to-purchase drive cash flow; viral coefficient + viral cycle time drive adoption).

**Five metric distinctions to keep in mind.**
- Qualitative vs. quantitative (quant answers "what/how much"; qual answers "why"; qual abhors aggregation but marinates in emotion).
- Vanity vs. actionable (a vanity metric only goes "up and to the right" and strokes ego; an actionable metric changes behavior).
- Exploratory vs. reporting (Rumsfeld's known-knowns/known-unknowns/unknown-unknowns; the "unknown unknowns" are where startup magic lives).
- Leading vs. lagging (leading metrics predict; lagging metrics explain the past; leading is better because you can still act).
- Correlated vs. causal (causal relationships between something you want and something you control let you change the future).

**Vanity metrics to avoid:** number of hits, page views, visits, unique visitors, followers/friends/likes, time on site/pages, emails collected, and number of downloads. The actionable counterpart of "total signups" is "percent of users who are active."

**False metrics.** Metrics tied to the wrong incentive (Seth Godin's "avoiding false metrics"): a salesperson begging for a 5/5 rating, or compensation tied to pipeline volume rather than closed deals. If a metric is not attached to a goal that changes behavior, it is a waste of time or self-deception.

**Business model = a flipbook of aspects.** A business model is a combination of five aspects: acquisition channel (how people find you), selling tactic (how you convert via money or scarcity/exclusivity), revenue source (direct payment or indirect like ads, referrals, data), product type (the value offered), and delivery model (how it reaches the customer). Freemium is a marketing tactic, SaaS is a delivery model, ads are a revenue source, none are "business models" by themselves. Mixing these "pages" produces thousands of permutations; turning a page is a pivot.

**Not all users are equal.** Segment real, valuable users from drive-by, curious, or detrimental ones. Don't fall victim to customer counting; optimize for good customers and match your payment/incentive model (freemium, free trial, pay up-front, discount, ad-funded) to your segmentation, time-to-paying-customer, ease of use, and incremental cost per drive-by user.

## Frameworks & models

**Dave McClure's Pirate Metrics (AARRR):** Acquisition (traffic, CPC, cost of acquisition), Activation (signups, completed onboarding, first use), Retention (engagement, daily/monthly active use, churn), Revenue (CLV, conversion rate, cart size), Referral (invites sent, viral coefficient, viral cycle time). Stages need not be strictly ordered.

**Eric Ries's Engines of Growth:**
- Sticky engine - get users to return; KPI: customer retention; also churn and usage frequency.
- Virality engine - get the word out; KPI: viral coefficient (>1 grows); also viral cycle time and type of virality.
- Paid engine - turn revenue into acquisition; knobs: customer lifetime value (CLV) and customer acquisition cost (CAC); also time to customer breakeven.
Focus on one engine at a time, but ultimately build all three.

**Ash Maurya's Lean Canvas.** A living one-page model (vs. a static business plan), with boxes treated as pass/fail experiments: Problem, Solution, Unique Value Proposition, Customer Segments, Channels, Unfair Advantage, Revenue Streams, Cost Structure, plus a Key Metrics box (not experimented on but recorded). Each box has relevant metrics (e.g., Problem → respondents who have/are aware of the need; Channels → leads per channel, viral coefficient, NPS).

**Sean Ellis's Startup Growth Pyramid.** What to do after product/market fit. The product/market-fit test: survey customers with "How would you feel if you could no longer use this product?"; if 40% or more say "very disappointed," you have fit and can scale. Stickiness comes before virality; virality before scale.

**The Long Funnel.** The modern funnel extends across social networks, sharing platforms, affiliates, and price-comparison sites; multiple visits and offline/online factors precede a conversion. Inject tracking into the initial signal to follow users.

**The Lean Analytics Stages and Gates (the book's own model).** Five stages, each with a gating metric to clear before advancing:
1. Empathy - get inside the market's head; validate a real problem and that a solution is likely to work (qualitative interviews, surveys, curated MVPs).
2. Stickiness - build a product good enough to engage users; gate on retention/engagement (avoid premature scaling like Color).
3. Virality - use word of mouth; optimize viral coefficient and cycle time; a force multiplier before paid acquisition.
4. Revenue - maximize and optimize monetization; pour some revenue back into acquisition.
5. Scale - grow the market through channels, verticals, geographies, and ecosystem; past product/market fit, analyzed quantitatively.
These map across business types (the book illustrates with a restaurant and an enterprise software company).

**The One Metric That Matters (OMTM).** The single number you focus on above all else for your current stage; it changes over time. Four reasons to use it: it answers your most important (riskiest) question; it forces a line in the sand and clear goals; it focuses the entire company (avoid Kaushik's "data puking"); it inspires a culture of experimentation (small-f failure is learning, the way to avoid big-F Failure). Capture everything, but focus on what's important. Moz reduced its KPIs to a few (Net Adds) on an investor's advice to stay focused.

**Drawing lines in the sand.** Pick a target number for the OMTM. Two right answers: what the business model requires (e.g., 10% must convert to paid), or, early on, an industry baseline of what's normal/ideal.

**The squeeze toy.** Optimizing one metric to its max reveals the next place to focus (an inflection point), just as squeezing a stress toy bulges it elsewhere.

**Model + Stage drives the metric.** The core operating loop: know your model, know your stage, pick the OMTM, optimize it, repeat. The book's matrix maps each of six models across stages to "the really big question" and the metrics that matter.

## Business model archetypes and their key metrics
- **E-commerce:** conversion rate, purchases per year, shopping cart size, abandonment, cost of customer acquisition, revenue per customer; plus keywords/search terms, recommendation acceptance, virality, email click-through, shipping time, stock availability. Distinguish traditional vs. subscription e-commerce.
- **SaaS:** attention, enrollment, stickiness, conversion (free/trial to paid and tier upgrades), revenue per customer, CAC, virality, upselling, uptime/reliability, churn, lifetime value. Monthly recurring revenue (MRR) is foundational; CLV and CAC are the two essential subscription metrics; customer acquisition payback (months) rolls up marketing efficiency, revenue, cash flow, and churn (Backupify target: 12 months or less; CLV:CAC of 5-6x is excellent).
- **Free mobile app:** downloads, average revenue per user (ARPU), average revenue per paying user (ARPPU), in-app purchase rate, acquisition costs, app-store ratings, rankings, day/week/month churn.
- **Media site:** traffic/visits/returns, ad revenue, click-through, impressions, cost per engagement, affiliate revenue, time on page (engagement with content in a repeatable manner).
- **User-generated content:** content creation rate, engagement funnel, spam rates, content and word-of-mouth sharing, primary acquisition channels.
- **Two-sided marketplace:** rate of inventory creation (sellers/listings), search type and frequency, listing quality, price elasticity, fraud rates, buyer and seller acquisition, transactions/commissions.

## Patterns

**OMTM discipline** - Intent: maintain focus by elevating one stage-appropriate metric above all others. When to use: especially early-stage; one engine/metric at a time. Trade-offs: must change the OMTM as the business evolves; track everything else but do not act on it all.

**Model + Stage selection of metrics** - Intent: choose the right metrics by intersecting business model with growth stage. When to use: deciding what to instrument and optimize next. Trade-offs: prescriptive; real businesses overlap models and stages.

**Lines in the sand / benchmarking** - Intent: set a target for a metric from the business model or an industry baseline so you know when to stop optimizing. When to use: before running an experiment; whenever a metric looks "bad." Trade-offs: baselines shift as a model goes from novel to mainstream; "average is not good enough."

**Line-drawing experiments** - Intent: pre-commit to the decision an experiment's result will trigger. When to use: every experimental metric. Trade-offs: requires honest pre-agreement on success criteria.

**Lean Canvas as living plan** - Intent: validate a one-page business model box by box via experiments. When to use: deciding what to build and continuously thereafter. Trade-offs: must be updated continuously, not a static artifact.

## Principles & best practices
- Be driven, not just data-collecting: for every metric ask "what will I do differently based on this?"
- Prefer ratios/rates that are comparative, understandable, and behavior-changing.
- Start qualitative (get out of the building) to find the "why," then quantify.
- Pick the OMTM for your current stage; display it prominently; change it as you progress.
- Draw a line in the sand from your model or an industry benchmark before experimenting.
- Validate assumptions in the right order: empathy → stickiness → virality → revenue → scale.
- Don't scale prematurely; stickiness before virality, virality before scale; inherent virality beats artificial.
- Spend less than about a third of customer revenue (CLV) on acquisition (CAC); turn on the paid engine only once sticky and viral.
- Segment users; optimize for good customers; don't count customers as a vanity metric.
- Benchmark, but remember average is not good enough.

## Anti-patterns & pitfalls
- Vanity metrics (hits, page views, raw signups, followers, downloads) and false metrics tied to the wrong incentive.
- Data puking: reporting too many numbers and losing focus.
- Premature scaling / premature virality / "growth at all costs" before product/market fit (e.g., Color).
- Lying to yourself about your stage (founders believe they are further along than they are).
- Over-optimizing a metric that cannot move (WP Engine's ~2%/month hosting churn is best-in-class) instead of moving on.
- Tracking lagging metrics only, or correlations mistaken for causation.
- Treating freemium/SaaS/ads as "business models" rather than tactics/delivery/revenue aspects.
- Settling for "average" CAC:CLV (~1:1 for consumer apps means you spend all you make).

## Decision guidance
- Identify your business model (or closest of the six) and your stage; intersect them to choose the OMTM and supporting KPIs.
- For the chosen metric, set a target from your business model; if unknown, use an industry baseline as a starting line in the sand.
- Sequence focus by stage: validate problem and solution (empathy) before building; prove stickiness (retention/engagement; aim ~5% weekly active-user growth near product/market fit; churn under 5%, ideally 2%) before virality; prove virality before turning on paid acquisition; optimize revenue before scaling.
- Use the product/market-fit survey (40% "very disappointed") as a gate before the Startup Growth Pyramid's scaling work.
- When a metric is maxed or matches best-in-class benchmarks, move to the next metric the squeeze toy reveals.
- Keep CAC well below CLV (acquisition under ~1/3 of customer revenue; for big-ticket CLV >$50K, 0.2-2% of CLV) before scaling the paid engine.

## Source attribution
- What makes a good metric, the five metric distinctions, vanity and false metrics, qualitative vs. quantitative: Lean Analytics, Ch. 2 (Croll and Yoskovitz, 2013).
- Lean Canvas usage: Ch. 3 and Ch. 5.
- Pirate Metrics, Engines of Growth, Lean Canvas metrics, Startup Growth Pyramid, the Long Funnel, the Lean Analytics Stages and Gates: Ch. 5.
- OMTM discipline, four reasons, drawing lines in the sand, the squeeze toy, Moz/Net Adds: Ch. 6.
- Business model flipbook, segmenting users, the six models: Ch. 7.
- E-commerce metrics: Ch. 8. SaaS metrics, MRR/CLV/CAC/payback, Backupify: Ch. 9. (Free mobile app, media, user-generated content, two-sided marketplace: Ch. 10-13, summarized via the model+stage matrix.)
- Lean Analytics stages defined, restaurant and enterprise examples: Ch. 14; per-stage detail Ch. 15-19.
- Model + Stage matrix: Ch. 20. Benchmarks / lines in the sand (WP Engine 2% churn, Startup Compass averages, Paul Graham 5-7%/week growth, CAC:CLV): Ch. 21.

## Recent developments (2024–2026)

- **Usage-based pricing went mainstream, reshaping SaaS metrics.** 2025 benchmark reports put usage-based pricing adoption at around 85%, up from roughly 28% in 2023. This shifts emphasis from flat MRR toward consumption metrics, net revenue retention (NRR), and expansion, and makes the book's "metrics come in pairs behind a fundamental metric" point more concrete (consumption and retention now drive recurring revenue). (Growth Unhinged / "2025 SaaS Benchmarks Report"; High Alpha SaaS Benchmarks, 2025)

- **Updated benchmark "lines in the sand."** 2025 reports give current reference numbers that refresh the book's 2013 benchmarks: median SaaS ARR growth around 19-21%, median CAC payback worsening to roughly 20 months, and top-performer NRR around 104-106%. Companies pairing high NRR with low CAC roughly double their growth and Rule of 40 scores. These are the kind of empirical "lines in the sand" the book advocates, just recalibrated for a higher-rate, post-2022 environment. (Pavilion B2B SaaS Benchmarks, 2025; Benchmarkit, 2025)

- **AI-native companies are growing about twice as fast.** Multiple 2025 benchmark sets report AI-native firms growing roughly 2x faster than traditional SaaS (often cited as ~100% vs ~50% at comparable stages), making "are you AI-native?" a new segmentation axis when interpreting whether a growth metric is good. (High Alpha, 2025; Growth Unhinged, 2025)

- **Product-led growth matured into product-led sales (hybrid).** The clean PLG-vs-sales-led split has largely collapsed into a hybrid "product-led sales" model; product-qualified leads (PQLs) are reported to convert roughly 5-6x better than marketing-qualified leads. This validates and extends the book's activation/retention-before-monetization sequencing, with PQL signal now a standard instrument. (T2D3, "The Great Recalibration," 2025; Pavilion, 2025)

- **AI is changing how metrics are produced, but measurement discipline lags.** Teams increasingly use AI for analytics (clustering feedback, drafting metric narratives, surfacing anomalies), but 2025 reporting notes many still measure AI's own impact only qualitatively, a live example of the book's "be driven, not just data-collecting" warning applied to AI adoption itself. (Growth Unhinged, 2025)

- **Core method stable.** OMTM discipline, the model + stage matrix, vanity-vs-actionable metrics, ratios/rates, and the empathy → stickiness → virality → revenue → scale sequence remain sound and widely taught. What has changed since 2013 is the prevailing pricing model (usage-based), the benchmark numbers, and the AI-native growth segment, all of which the book's "know your model, know your stage, pick the metric" loop accommodates.
