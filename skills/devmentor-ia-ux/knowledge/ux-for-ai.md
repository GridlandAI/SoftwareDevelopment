# UX for AI

**Domain:** 14 - Information Architecture and UX
**Primary sources:** UX for AI: A Framework for Designing AI-Driven Products (Nudelman & Kempka, 2025)

## Overview

AI-driven products break many assumptions of conventional UX. The system is probabilistic, not deterministic; it can be confidently wrong (hallucinate); its "accuracy" number says little about real-world value; and the design problem now spans three coupled pillars at once: the user interface, the AI/ML model, and the data. Designing these products well requires new framing tools, a catalog of interaction patterns specific to AI and generative interfaces, explicit handling of uncertainty, error, and trust, and an evolved, AI-inclusive design process. This file captures that framework concretely so an agent can recognize the right pattern for a given AI UX problem and reason about its trade-offs.

The single source, Nudelman and Kempka (2025), is current. Its through-line: AI is a "wicked problem" and is too important to leave to data scientists; the best AI is *augmented* intelligence (human plus AI), and UX is what turns raw model capability into a product people can actually use and trust. The book is organized as Framing the Problem, AI Design Patterns, Research for AI Projects, and Bias and Ethics, anchored by a running "Life Clock / Life Copilot" design exercise.

## Core concepts

**The three pillars of AI-driven design.** Every AI product continuously up-levels three coupled things: the user interface, the AI model, and the data. Unlike the cloud era, where UX could ignore the back end, AI UX must reason about model behavior and data because they directly shape the experience.

**Accuracy is not value (the confusion matrix vs. the value matrix).** A model's accuracy is a single number that hides what matters. The *confusion matrix* counts the four outcomes: true positives, true negatives, false positives, false negatives. The *value matrix* (Arijit Sengupta) augments each cell with its real-world business/human cost or benefit. The ROI of an AI decision is the impact of each error type multiplied by how often it occurs. This is the central reframing: UX and product, not data science alone, must decide what each outcome is worth and design the interaction accordingly.

**Hallucination and the cost of being wrong.** Because AI will sometimes be wrong, the key design question for any AI action is: "How often will it be wrong, and what is the impact of a false positive?" A near-zero-cost false positive (a cheap query) can execute immediately; a catastrophic one (texting the wrong person, dosing a patient) demands confirmation before acting. The value matrix supplies the numbers to decide.

**Picking the right use case.** The most common failure is aiming AI at the wrong problem. Red flags: trying to replace a trained expert, ignoring cost vs. benefit, having no ML training data, treating the question the model answers as interchangeable, and skipping user research because "we have a subject-matter expert." The fix is to ask a better question and design for human-plus-AI value.

**Storyboarding and the digital twin.** Storyboarding (establishing shot, things, people, faces, transitions) frames the AI use case as a narrative before any UI exists. A *digital twin* is a digital representation of the physical components of the system, an essential modeling exercise for grounding what the AI must observe and act on.

**Augmented intelligence over automation.** The recurring stance: design AI to augment human capability, keep a human in the loop for consequential decisions, and treat the human-AI pair as the unit of design.

**Information architecture is not dead.** Despite the "canvas" pattern and chat-first interfaces, AI-first applications still need IA. The book redesigns Amazon.com as AI-first (analysis overview pages, category analysis, AI-first search and item detail) to show that structure, findability, and navigation remain essential; AI changes the surface, not the need for organization.

## Frameworks & models

**The seven LLM design patterns.** A core catalog for any LLM/SLM interface, aimed at keeping the model a "servant, not master" and ensuring it does what the user intended:
1. **Restating** - the AI reports back what it understood the input to be ("Showing results for X"), so there is no ambiguity. Whether to restate before acting depends on the value matrix.
2. **Auto-complete** - "prepones" the restatement by suggesting correct concepts and vocabulary as the user types, removing confusion before it arises; can label fields as changeable vs. unmatched.
3. **Talk-back** - richer than restating: the AI explains what went wrong and why, asks follow-up questions, and guides exploration; usually needs a chat interface.
4. **Initial suggestions** - prompts shown before the user acts, ideally tuned to the selected data or prior conversation rather than generic, to reduce the blank-page problem.
5. **Next steps** - post-query suggestions inferring the likely next question, powered by deep analysis of the results plus continuous reinforcement learning from what users click.
6. **Regen tweaks** - one-click controls (vary subtle/strong, region, viewpoint, parameters) for the creative-generation flow where the user expects to regenerate and refine output.
7. **Guardrails** - content moderation and policy enforcement on what the model will and will not produce; necessary but never perfect (clever prompting can bypass them, and once data is in the model there is no reliable RBAC over it).

**Chat/exploration flow vs. creative-generation flow.** Two opposite polarities with different defaults. In *chat/exploration* the output is assumed mostly correct, the model runs "cold" (low temperature) for consistency, and **next steps** are most useful. In *creative generation* the output is assumed not yet right, the model runs "hot" for variety, and **regen tweaks** are most useful. Choosing the polarity sets the temperature and the pattern.

**Copilot UX best practices.** For SaaS copilots: more important tasks need more screen real estate (side panel for light, large overlay for medium, full page for heavy). Copilots are stateful, often use a fine-tuned model plus plug-ins for continuous learning about the specific system, keep the IA straightforward and chat-focused, and offer promptbooks so users need not craft prompts from scratch. Reporting (meeting summaries, executive summaries, pinboards) is one of the most important copilot use cases, with choices about auto-vs-manual report content and built-in security/privacy.

**AI-specific data UX.** Patterns for AI-driven analytics: forecasting with line graphs (linear/nonlinear regression, R-squared, seasonality, aggregate forecasting); anomaly detection across four types (point, change-point, contextual, curve-shape) with static vs. dynamic thresholds; and degree-of-interest (DOI) sort algorithms with "eye meat" dynamic dashboards, while watching for bias in AI recommendations.

**UX for agentic AI.** Agentic systems use a *supervisor (reasoning) agent* that spins up specialized semi-autonomous *worker agents* (the "ant colony" analogy). The defining property vs. plain AI is *autonomy*: the human issues a general request, not precise instructions. The collaborative flow (illustrated with an AWS CloudWatch investigation): human launches an investigation -> supervisor spawns workers that asynchronously examine parts of the system -> workers return "suggested observations" -> human accepts/rejects relevant ones -> supervisor digs deeper based on feedback -> supervisor delivers a hypothesis (root cause) -> on acceptance, provides fix and prevention next steps. The human reviews and steers; internals stay hidden.

**The AI Humanifesto (Paul Bryan).** A five-concept ethics framework for pro-human AI design:
- **Control** (governance, accountability) - human oversight, user autonomy to modify or opt out, transparency of algorithms, and user control over their data.
- **Trust** (transparency, reliability, explainability) - consistent performance, ownership of mistakes, responsible data handling, openness/collaboration, and accountability for harm.
- **Diversity** (security, privacy, robustness) - inclusive datasets, acknowledging bias and gaps, evolving with society, breaking barriers, cultural sensitivity.
- **Safety** (inclusion, bias, equity, fairness) - privacy and data security, consent and education, physical and emotional wellbeing (no addictive/isolating design), risk management, balancing innovation with safety.
- **Balance** (sustainability, harmony, wellbeing, human empowerment) - balancing AI power with human creativity, societal wellbeing, and environmental sustainability (e.g. AI's energy/compute footprint).

**The AI-inclusive user-centered design process.** Conventional linear UX diagrams (define -> ideate -> prototype -> test -> release) misrepresent the messy reality, and even more so for AI. The new process is rapid, flexible, and continuously up-levels the three pillars (UI, model, data). It introduces the *spike* (a quick technical/feasibility probe), centers data and the customer throughout, and changes UX's role from polishing UI to strategic, cross-functional design. RITE (Rapid Iterative Testing and Evaluation) is the cornerstone research method, producing solutions, not just reports, on the simplest appropriate prototypes that change rapidly.

**Bookending / disciplined brainstorming.** A divergent ideation method: seed designs from existing products and other domains, sketch many quickly (record: 12 designs in 10 minutes), and stop polishing the moment you start fiddling with colors and labels, to reach novel directions fast.

**Shorthand UX design notation as AI prompt.** A compact notation that doubles as a prompt to take a design straight to working code, reflecting the shift in "human work" as AI automates UI production.

## Patterns

(Also appended to PATTERN-CATALOG.md under Domain 14.)

- **Value matrix** - Intent: assign real-world cost/benefit to each confusion-matrix outcome so the team designs around impact, not accuracy. When to use: any AI decision where errors have unequal consequences. Trade-offs: requires honest cost estimates and cross-functional input; replaces a single misleading accuracy number with actionable ROI. *(Nudelman & Kempka)*
- **Restating** - Intent: have the AI echo its interpretation of the input before or instead of acting. When to use: ambiguous natural-language input, especially before consequential actions. Trade-offs: adds a step; essential when false positives are costly, skippable when they are cheap. *(Nudelman & Kempka)*
- **Auto-complete (preponing)** - Intent: suggest valid concepts/vocabulary as the user types to prevent miscommunication before it happens. When to use: query and search interfaces over known data. Trade-offs: expensive to build well; dramatically reduces missed queries. *(Nudelman & Kempka)*
- **Talk-back** - Intent: have the AI explain errors, ask clarifying questions, and guide exploration. When to use: complex, exploratory, conversational tasks. Trade-offs: needs a chat interface and screen space; far richer than passive restating. *(Nudelman & Kempka)*
- **Initial suggestions** - Intent: seed the interaction with context-tuned prompts to beat the blank page. When to use: entry points of any generative or query tool. Trade-offs: generic suggestions add little; value comes from tuning to data and history. *(Nudelman & Kempka)*
- **Next steps** - Intent: infer and surface the user's likely next question after a result, using deep analysis plus click-based reinforcement learning. When to use: chat/exploration flows. Trade-offs: requires continuous retraining; powerful for sustaining exploration. *(Nudelman & Kempka)*
- **Regen tweaks** - Intent: provide quick one-click controls to regenerate creative output with variation. When to use: creative-generation flows (image/text generation). Trade-offs: only fits "hot" creative flows, not consistency-seeking ones. *(Nudelman & Kempka)*
- **Guardrails** - Intent: enforce content and policy limits on AI output. When to use: any user-facing generative system. Trade-offs: never fully bypass-proof; once data is in the model there is no reliable access control, so isolate truly private data in a locked-down model. *(Nudelman & Kempka)*
- **Confirm-before-consequential-action (value-matrix gated)** - Intent: require explicit confirmation before AI executes an action whose false positive is costly. When to use: sending, paying, deleting, dosing, anything irreversible or high-impact. Trade-offs: friction; gate it on the value matrix so cheap actions stay frictionless. *(Nudelman & Kempka)*
- **Supervisor + worker agents (agentic flow)** - Intent: let a reasoning agent orchestrate specialized semi-autonomous workers while the human steers via accept/reject on suggested observations. When to use: complex investigation, troubleshooting, multi-step research/automation. Trade-offs: autonomy reduces user control and can surface wrong hypotheses; keep the human in the loop reviewing evidence and conclusions. *(Nudelman & Kempka)*
- **Human-in-the-loop control over autonomous AI** - Intent: ensure humans can understand, override, and stop AI action, especially in safety-critical systems. When to use: any consequential or autonomous AI (the Lion Air MCAS crash is framed as a UX-for-AI failure where AI and pilots fought for control). Trade-offs: may slow full automation; prevents catastrophic loss of human control. *(Nudelman & Kempka)*
- **Bookending / disciplined brainstorming** - Intent: rapidly diverge on AI UI ideas by borrowing from other products and domains and stopping before polish. When to use: early ideation for AI features. Trade-offs: produces rough sketches needing later refinement; reliably reaches novel directions. *(Nudelman & Kempka)*
- **Copilot real-estate tiering** - Intent: size the copilot surface to task importance (side panel / large overlay / full page). When to use: embedding a copilot in a SaaS product. Trade-offs: full-page surfaces dominate the host UI; match to how heavy the task is. *(Nudelman & Kempka)*

## Principles & best practices

- Design the three pillars together: UI, model, and data are inseparable in AI products.
- Replace "accuracy" with the value matrix; decide what each error type is worth before designing the interaction.
- Gate AI actions on the cost of being wrong: cheap false positives can auto-execute; costly ones require restating or confirmation.
- Pick the right use case first; do not aim AI at replacing experts or at problems with no training data or no clear cost/benefit.
- Choose the interaction polarity (chat/exploration vs. creative generation) deliberately; it sets temperature and which patterns apply.
- Keep a human in the loop for consequential and autonomous decisions; design for understandability, override, and stop.
- Preserve information architecture in AI-first apps; chat and canvas do not remove the need for structure and findability.
- Apply the AI Humanifesto: build for control, trust, diversity, safety, and balance, including environmental sustainability.
- Treat all AI as biased; inspect datasets for gaps and inspect recommendations for bias.
- Use RITE and rapid iteration; prototype the simplest thing that answers the current question and change it fast.
- Remember the best AI is augmented intelligence: optimize the human-AI pair, not the model in isolation.

## Anti-patterns & pitfalls

- **Optimizing for accuracy alone**, ignoring the unequal real-world cost of false positives and negatives.
- **Auto-executing consequential AI actions** without restating or confirmation (the "text your boss the wrong message" failure).
- **Replacing a trained expert with AI** or presuming AI will tell experts how to do their job (a stated red flag).
- **Skipping user research** because a subject-matter expert is available.
- **Assuming IA is dead** in AI-first products and shipping structureless chat/canvas interfaces.
- **Trusting guardrails as airtight**, or assuming data in a shared model can be access-controlled; clever prompting bypasses guardrails and exposes data.
- **Designing autonomous AI that fights the human for control** (Lion Air MCAS) instead of keeping humans able to understand and override.
- **Ignoring bias, environmental cost, and addictive/isolating design**, violating the Humanifesto's diversity, balance, and safety concepts.
- **Forcing a linear UX process** onto the inherently messy, three-pillar AI design reality.

## Decision guidance

- Before building, run the value matrix: estimate how often the model is wrong and the cost of each error type; compute ROI per design option. Let that decide whether to auto-execute, restate, or confirm.
- Pick the flow polarity: if output is assumed mostly correct and you want consistency, use chat/exploration (cold model, next steps); if you want creative variety, use creative generation (hot model, regen tweaks).
- Layer the LLM patterns by need: auto-complete and initial suggestions to start an interaction, restating/talk-back to confirm and guide, next steps or regen tweaks to continue, guardrails throughout.
- For agentic systems, keep the human as the steering reviewer (accept/reject observations, approve hypotheses) and hide internals; escalate human control as consequences rise.
- Size copilot real estate to task weight; keep its IA chat-focused and offer promptbooks.
- Check every AI feature against the Humanifesto: Can the user understand, control, and opt out (Control)? Is it reliable and does it own its mistakes (Trust)? Is the data inclusive and unbiased (Diversity)? Is it safe, private, and non-addictive (Safety)? Is it sustainable and human-empowering (Balance)?
- Use bookending to diverge quickly, then RITE to converge on the simplest prototype that answers the open question.

## Source attribution

- *All content in this file* - Nudelman & Kempka, *UX for AI: A Framework for Designing AI-Driven Products* (2025). Specifically: framing failures and use-case selection, storyboarding, digital twin, confusion matrix and value matrix (Sengupta), copilot best practices and reporting (Ch. 6-8); the seven LLM design patterns and chat-vs-creative flows (Ch. 9); AI search, DOI sort, and AI-first IA (Ch. 10-12); forecasting and anomaly detection (Ch. 13-14); UX for agentic AI and the AWS CloudWatch flow (Ch. 15); bookending/MUSE (Ch. 16); the AI-inclusive design process and RITE (Ch. 17, 19); AI and UX research automation (Ch. 18); bias and the AI Humanifesto by Paul Bryan (Ch. 21-22); the Lion Air MCAS case as a UX-for-AI failure (Introduction); and augmented intelligence as the closing stance (Ch. 23).

## Recent developments (2024-2026)
The 2025 primary source remains current; these online sources extend it.

- **Generative UI moves from concept to pattern catalog.** The dominant 2025 framing is interfaces that adapt layout, content, tone, and interaction in real time per user. Practitioners now name five recurring AI UX patterns: predictive design, generative assistance, adaptive personalization, background automation, and conversational interfaces, all aimed at reducing the number of user interactions. (cqlsys.com, "How Generative AI is Transforming UI/UX Design in 2025"; LogRocket Blog, "AI-driven UX design patterns," 2025; Shape of AI pattern library, shapeof.ai, 2025.)
- **Pattern libraries specifically for AI UX matured.** "The Shape of AI" (shapeof.ai) became a widely referenced open catalog of UX patterns for AI features (wayfinding, trust, input, output, governance), complementing Nudelman and Kempka's seven LLM patterns. (shapeof.ai, 2025.)
- **Dark-pattern risk in generative UI is now an explicit concern.** Surveys report a large share of designers fear adaptive/generative UI optimized purely for conversion will industrialize dark patterns under the banner of "UX optimization," reinforcing the book's Humanifesto stance. (cqlsys.com, 2025; LogRocket, 2025.)
- **Vibe coding reshapes the design-to-code boundary.** Academic and practitioner work on "vibe coding" (natural-language prompts producing working UI) studies how product teams perceive AI-assisted design and development, validating the book's point that the "human work" in UI production is shifting toward intent and curation. (arXiv 2509.10652, "Vibe Coding for Product Design," 2025.)
- **Jakob Nielsen's running UX commentary tracks the shift.** Nielsen's 2025 "UX Roundup" pieces argue AI is a paradigm shift for UX comparable to the GUI, emphasizing that "useful AI sells" and that measurable user value, not novelty, is the differentiator. (jakobnielsenphd.substack.com, "UX Roundup," Dec 2025.)
- **Agentic UX is an active design frontier.** Industry guidance in 2025 stresses designing for human steering of semi-autonomous agents (accept/reject, transparency, escapability), matching the book's supervisor/worker model. (Eleken, "Generative AI UX," 2025.)
