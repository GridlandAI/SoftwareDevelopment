# Design Psychology and Cognition

**Domain:** 14 - Information Architecture and UX
**Primary sources:** 100 Things Every Designer Needs to Know About People (Weinschenk, 2nd ed., 2020); The Handbook of Formal Methods in Human-Computer Interaction (Weyers, Bowen, Dix, Palanque et al., eds., 2017)

## Overview

Every interface is used by a human brain that perceives, reads, remembers, attends, decides, and feels in predictable ways. Design psychology is the body of empirical findings about those mechanisms, translated into actionable design guidance. A system that ignores how people actually think will fail no matter how clean its architecture, because the user, not the code, is the slowest and most error-prone component in the loop. For software practitioners this matters across the whole SDLC: it shapes requirements (what users can realistically do), interaction design (what loads they can carry), error handling (the mistakes they will inevitably make), and onboarding (how they form mental models).

This file distills two complementary lenses. Weinschenk gives the descriptive, evidence-based account of human cognition and behavior, organized around how people see, read, remember, think, focus, are motivated, behave socially, feel, make mistakes, and decide, with the design implication of each. The Handbook of Formal Methods in HCI gives the rigorous, prescriptive lens: how to model interactive systems and even the human operator formally, so that usability and safety properties can be reasoned about mathematically rather than only discovered in testing. Together they cover both "how people work" and "how to reason rigorously about systems built for those people."

## Core concepts

### How people see

- **What you see is not what your brain gets.** Vision is reconstruction, not recording. The brain fills in, infers, and creates shortcuts from past experience and expectation. Optical illusions are the visible edge of a constant process. Implication: do not assume users perceive what is literally on the screen.
- **Peripheral vision carries the gist.** People use peripheral vision more than central vision to get the overall sense of a scene or page, and keep central (foveal) vision for detail. Keep critical status and emotional cues where peripheral vision will catch them; do not bury the main message in a spot that only foveal vision reaches.
- **Pattern recognition and the geon theory.** Objects are recognized by decomposing them into simple geometric primitives (geons). This is why simplified, iconic forms are read faster than photorealistic detail.
- **Dedicated face and feature processing.** Specific brain regions handle faces and simple visual features (color, motion, orientation). Faces grab attention and convey emotion instantly; this is powerful and easily overused.
- **Scanning follows expectation.** People scan screens based on past experience and mental models of where things should be (logo top-left, search top-right). Violating these layouts forces conscious effort. Under difficulty, people narrow their visual field and miss things outside it.
- **Affordances and signifiers.** People look for cues that tell them what to do with an object. Perceived affordances (what the design appears to allow) matter more for usability than real ones. Flat design that strips affordance cues (underlines, button shading) raises the cost of figuring out what is clickable.
- **Change blindness.** People can miss large changes in their visual field, especially across a flicker, scroll, or page transition. Do not rely on a silent visual change to communicate something important.
- **Grouping (proximity).** Things close together are believed to belong together. Layout spacing is a semantic statement.
- **Color cautions.** Red and blue together strain the eyes (chromostereopsis). Roughly nine percent of men and half a percent of women are color blind, so color must never be the only carrier of meaning. Color connotations differ across cultures.

### How people read

- **All-caps is not inherently hard to read,** but it is read more slowly because the familiar word-shape pattern is lost and we are simply less practiced at it. Reserve caps for short labels.
- **Reading is not comprehending.** Decoding text and understanding it are separate. Titles, headlines, and clear structure drive comprehension; what people remember depends on the point of view they read from.
- **Font matters.** Hard-to-read fonts make people judge the underlying task as harder. Size matters: there is a usable minimum, and screen reading is harder than paper.
- **Line length tension.** People read faster with longer lines but prefer shorter ones. Choose based on whether speed or comfort dominates the task.

### How people remember

- **Working memory is tiny and fragile.** It is easily disrupted by interruption and competes with sensory input. Do not require users to hold information in their head across steps; show it.
- **The "four item" rule.** People reliably hold only about four items at once (the famous "seven plus or minus two" is the looser folk version). Chunking turns four into effectively more. The limit applies to retrieval too.
- **Use it or lose it; schemas.** Information sticks only through use and repetition, which physically rewires the brain. Experts store knowledge as rich schemata, which is why they chunk and retrieve so much more.
- **Recognition beats recall.** It is far easier to recognize information presented on screen than to recall it from memory. Show options rather than demanding remembered commands (this underpins menus over command lines, and large mega-menus).
- **Memory is reconstructive and lossy.** People rebuild memories each time, introducing errors; the most vivid "flashbulb" memories are often wrong; forgetting is normal and even useful. This makes retrospective self-report (interviews, surveys) unreliable and argues for observing behavior.

### How people think

- **Bite-sized chunks and progressive disclosure.** People process information best in small pieces. Reveal detail progressively; what matters is mental effort, not click counts.
- **Cognitive, visual, and motor loads differ.** Not all loads are equal; cognitive load is most expensive, motor load (governed by Fitts's Law) is cheapest. Trade an extra click (motor) to avoid a hard decision (cognitive). Sometimes you deliberately increase load to slow people down before a dangerous action.
- **Minds wander about 30 percent of the time,** which is both a risk (lost context) and a resource (incubation, creativity).
- **Mental models and conceptual models.** People build a mental model of how something works and interact with the conceptual model your design presents. Mismatch between the two is a core source of usability failure.
- **Stories, examples, categories.** People process information best in story form (stories imply causation and persuade), learn best from examples, and are driven to categorize. If you do not provide categories, users invent their own, so how well it is organized matters more than who organized it.
- **Belief defense.** The more uncertain people are, the harder they defend their ideas; they screen out information that does not fit their beliefs. Asking for small commitments shifts behavior more reliably than confronting beliefs head-on.
- **Flow and culture.** People can enter a flow state of deep absorption. Culture shapes thinking (relational/collectivist East vs. individualistic West), visible even in brain scans.

### How people focus attention

- **Attention is selective and largely unconscious.** People habituate to the constant and notice the salient and the changing. Well-practiced skills run without conscious attention, which frees capacity but lets automatic errors slip in.
- **Sustained attention lasts about ten minutes;** salient cues, expectations of frequency, and goal relevance steer it.
- **Multitasking is mostly a myth.** People are far worse at it than they think; what feels like multitasking is rapid, costly switching.
- **What grabs attention:** danger, food, sex, movement, faces, and stories are evolutionarily privileged and seize attention almost involuntarily.

### What motivates people (see also the patterns section)

Motivation research overturns several "tried and true" assumptions. Key findings: people accelerate toward a visible goal (goal-gradient effect) and focus on what remains, not what is done; variable rewards drive the most repeated behavior; dopamine fuels seeking (not pleasure) and is amplified by unpredictability and small, cued information bursts, creating compulsive loops; intrinsic rewards and the drive for progress, mastery, autonomy, and social connection outlast extrinsic rewards, which can even backfire; people follow social norms, are inherently efficiency-seeking ("lazy"/satisficing), and are more motivated to compete when competitors are few.

### People are social animals

- **Dunbar's number (~150)** bounds stable strong-tie relationships.
- People are wired for **imitation and empathy** (mirror systems); doing things together bonds them; they expect online interactions to obey social rules and respond to machines socially.
- **Stories and anecdotes persuade more than data alone;** emotion is required to decide at all ("if people can't feel, they can't decide").

### How people feel

- Emotion drives engagement and trust. People judge **trust first by look and feel**; are programmed to enjoy surprise; are happier when busy and when shown pastoral/natural scenes; like things more when they are harder to achieve (effort justification); mispredict the intensity and duration of future emotional reactions; and want the familiar when sad or scared.

### People make mistakes

- **There is no fail-safe product; people will always err,** especially under stress. Not all mistakes are bad (some reveal better paths). Errors are predictable in type, and people use different error-recovery strategies. Design for prevention, graceful recovery, and constructive error messages rather than assuming flawless use.

### How people decide

- **Most decisions are made unconsciously,** and the unconscious often "knows" first. People want more choices and information than they can actually process, and conflate choice with control. They may value time over money; mood colors decisions; under uncertainty they defer to others; and they believe others are more easily influenced than themselves. Physical presence of a product raises its perceived value.

### Formal methods and the rigorous lens

- **Modelling and formal modelling.** A model is a deliberately simplified, abstracted view of a system captured in a notation. Models let designers abstract from detail, focus on chosen aspects, communicate with stakeholders, and break complex systems into manageable parts. Notations range from *informal* (natural language, loose diagrams, inherently ambiguous) through *semi-formal* (precise syntax but no formal semantics, e.g. UML state diagrams) to *formal* (precise syntax and mathematically defined semantics, e.g. Petri nets, process calculi, automata, Z, algebraic data types).
- **Verification and validation.** Formal models can be analyzed mathematically to detect flaws before implementation, in an iterative construct-analyze-amend cycle. The three core verification techniques are *theorem proving* (deductive proof, usually requiring analyst guidance), *model checking* (exhaustive automated search of a finite state space, producing counterexamples when a property fails), and *equivalence checking* (proving two system representations behave identically, e.g. that the implementation matches the task model).
- **Interactive-system property classes.** Beyond generic dependability, formal HCI focuses on interaction-specific properties: *visibility* (what the UI shows and how, the user's perception), *reachability* (what can be done at the UI and how), and *reliability* (the behavior of the underlying system). Predictability and human-error identification are recurring concerns.
- **Modelling the user.** Verification can include a formal model of the human as just another black-box component (the USER relation) wired to the DEVICE relation, with the user's perceptions equated to device outputs and actions to device inputs. The interesting middle ground is *cognitively plausible behavior*: rather than assuming an *angelic* user (always does the right thing) or a *demonic* one (does anything, used for safety/security analysis), model the realistic bounds of human cognition (limited working memory, single focus of attention, reliance only on the visible system image). Human error then emerges naturally from plausible behavior placed in an inappropriate situation, and the analysis stays tractable by pruning the search space to behavior real people actually exhibit.

## Frameworks & models

**Fitts's Law.** Time to acquire a target is a function of its distance and size: bigger and closer targets are faster and less error-prone. Governs button sizing and placement; the cheapest load to add or remove.

**Hick's Law (Hick-Hyman).** Decision time grows with the number of choices, for simple decisions. Limit options in time-sensitive simple choices; less applicable as decisions grow complex.

**Goal-gradient effect.** Motivation rises as a visible goal nears; even the *illusion* of progress (a pre-stamped loyalty card) accelerates behavior. Show progress and emphasize what remains.

**Operant conditioning / reinforcement schedules.** Behavior frequency depends on the reward schedule. Continuous rewards establish a new behavior; a *variable-ratio* schedule (unpredictable, like a slot machine) produces the most persistent repetition. Useful, and ethically fraught.

**Dopamine seeking loop.** Dopamine drives *wanting* and *seeking*, not liking; unpredictable, cued, small information bursts (notifications, short messages) create self-reinforcing loops. Designers can build these loops; users can break them by removing cues.

**Mental model / conceptual model alignment.** Design the conceptual model the interface presents to match users' existing mental models; intentional mismatch (for genuinely new paradigms) must be taught explicitly.

**The angelic-demonic continuum (formal HCI).** A spectrum of assumptions about user behavior in verification, from "always correct" to "anything possible," with cognitively plausible modelling occupying the realistic middle.

**Model-construct-analyze cycle (formal HCI).** Iteratively build a formal model, verify properties, amend the model or system to remove defects, and re-verify until results match expectations.

## Patterns

- **Progressive disclosure** - Intent: reveal information and options in stages so working memory and attention are not overwhelmed. When to use: complex forms, settings, onboarding, any task with optional depth. Trade-offs: hides functionality (discoverability cost); count mental effort, not clicks. *(Weinschenk)*
- **Chunking** - Intent: group information into a small number of meaningful units (around four) to fit working-memory limits. When to use: phone numbers, IDs, menus, instructions. Trade-offs: wrong grouping misleads; recognition aids (mega-menus) can safely exceed the limit because they avoid recall. *(Weinschenk)*
- **Recognition over recall** - Intent: present choices on screen instead of requiring remembered input. When to use: navigation, command access, form input. Trade-offs: consumes screen space; far lower cognitive load. *(Weinschenk)*
- **Show progress / emphasize what remains** - Intent: exploit the goal-gradient effect to sustain motivation through multi-step tasks. When to use: checkouts, wizards, profile completion, courses. Trade-offs: motivation can plummet right after the reward (post-reward reset); plan follow-up engagement. *(Weinschenk)*
- **Variable reward** - Intent: use unpredictable reinforcement to maximize repeat engagement. When to use: engagement-driven features. Trade-offs: powerful but manipulative; can foster compulsive use, raising ethical concerns. *(Weinschenk)*
- **Design for inevitable error** - Intent: assume users will make mistakes and provide prevention, easy reversal, and constructive recovery. When to use: any destructive or consequential action. Trade-offs: confirmations add friction; calibrate to the cost of the error. *(Weinschenk)*
- **Story-form communication** - Intent: frame information as narrative to aid processing, memory, and persuasion. When to use: onboarding, marketing, explaining causation, case studies. Trade-offs: can imply causation that is not there; keep honest. *(Weinschenk)*
- **Social proof / norms** - Intent: change behavior by showing what comparable others do. When to use: adoption, conservation, completion nudges. Trade-offs: backfires if the norm is undesirable or the comparison feels false. *(Weinschenk)*
- **Cognitively-plausible user model in verification** - Intent: include a bounded, realistic human model in formal analysis so usability and human-error problems emerge from proof, not just testing. When to use: safety-critical interactive systems (medical, aviation, nuclear). Trade-offs: building the user model is effortful; results are more persuasive because each problem links to an explanation of why it occurs. *(Weyers et al.)*

## Principles & best practices

- Design for the brain users actually have, not an idealized rational processor.
- Never make color the sole carrier of meaning; respect color-blindness and cultural color meaning.
- Respect working-memory limits: carry context forward visually, do not make users remember across steps.
- Prefer recognition to recall; show, do not ask people to remember.
- Match the conceptual model to users' mental models; teach explicitly when you must break expectation.
- Minimize cognitive load first, then visual, then motor; spend cheap motor load to save expensive cognitive load.
- Treat error as certain: prevent, allow reversal, and write recovery-oriented messages.
- Observe behavior over self-report; memory and introspection are unreliable.
- Use intrinsic motivators (mastery, autonomy, progress, connection) for durable engagement; extrinsic rewards can crowd them out.
- In safety-critical systems, complement testing with formal modelling and a realistic (not angelic, not demonic) user model.

## Anti-patterns & pitfalls

- **Assuming users read.** They scan and satisfice; walls of text go unread.
- **Color-only signaling** (status conveyed only by red/green), failing color-blind users.
- **Relying on silent visual change** to communicate, defeated by change blindness.
- **Counting clicks instead of measuring mental effort,** producing flows that are technically short but cognitively brutal.
- **Designing for the angelic user,** assuming flawless, trained operation and blaming users when reality intrudes, especially dangerous in safety-critical contexts.
- **Over-rewarding with money/extrinsic incentives,** which can extinguish the intrinsic motivation that sustained the behavior.
- **Trusting retrospective self-report** as ground truth for what users did or will do.
- **Engineering compulsive dopamine loops** without regard for user wellbeing.

## Decision guidance

- When a task feels "obvious to us but confusing to users," suspect a mental-model mismatch before adding instructions.
- When deciding how much friction to add before an action, weigh the cost of the error (Weinschenk's load trade-off): high-cost, irreversible actions justify a confirmation step; trivial actions do not.
- When choosing a research method, prefer observation/usability testing for what people *do*; use interviews/surveys cautiously for attitudes, knowing memory reconstructs and self-report misleads.
- When the system is safety-critical (medical, aviation, finance, nuclear), escalate from testing alone to formal modelling: use model checking for exhaustive property verification, and add a cognitively plausible user model to surface human-error pathways.
- When motivating repeat behavior, default to intrinsic and progress-based mechanisms; reserve variable-reward loops for cases where the ethics are clearly acceptable.
- Choose notation formality by stakes: informal sketches for early ideation, semi-formal models (UML, state diagrams) for communication, formal notations only where the cost of failure or ambiguity is high enough to justify the rigor.

## Source attribution

- *How people see, read, remember, think, focus; motivation; social behavior; emotion; mistakes; decision-making; all 100 principles and their design implications* - Weinschenk, *100 Things Every Designer Needs to Know About People* (2nd ed., 2020). Specific frameworks cited (Fitts's Law, Hick's Law, goal-gradient, operant conditioning, dopamine seeking, satisficing) are drawn from her synthesis of the underlying research.
- *Formal modelling, verification and validation, theorem proving / model checking / equivalence checking, property classes (visibility, reachability, reliability), modelling the user, the angelic-demonic continuum, cognitively plausible behavior* - Weyers, Bowen, Dix, Palanque et al. (eds.), *The Handbook of Formal Methods in Human-Computer Interaction* (2017), especially Chapter 1 (State of the Art) and Chapter 8 (Modelling the User).

## Recent developments (2024-2026)
The cognitive science (perception, memory limits, attention, error, Fitts/Hick, dopamine loops) is stable and slow-changing, as the source notes ("technology evolves fast, humans evolve slowly"). The 2024-2026 changes are regulatory and ethical applications of these findings.

- **Dark-pattern and persuasion concerns moved into regulation and active design debate.** As AI makes interfaces adaptive and personalized, designers report widespread fear that conversion-optimized generative UI will industrialize manipulative patterns (variable-reward and dopamine-loop mechanics applied without regard for wellbeing), exactly the ethical hazard this file flags. (cqlsys.com, "How Generative AI is Transforming UI/UX Design in 2025"; LogRocket Blog, "AI-driven UX design patterns," 2025.)
- **Accessibility findings are now legally enforced in the EU.** The European Accessibility Act took effect June 28, 2025, mandating EN 301 549 / WCAG 2.1 AA (WCAG 2.2 AA expected as the standard updates). The cognitive and perceptual guidance here (never color-only signaling, recognition over recall, perceivable affordances, error prevention/recovery) maps directly onto enforceable obligations. (levelaccess.com, "EAA Compliance Guide," 2025; en.wikipedia.org, "European Accessibility Act.")
- **Cognitive-load thinking applies to AI interactions.** Practitioner guidance for AI features stresses reducing the number and difficulty of decisions (fewer interactions, good defaults, confirmation gated to consequence), a direct restatement of Weinschenk's load trade-off in the agentic context. (LogRocket, 2025; shapeof.ai, 2025.)
- **Formal-methods and human-error modeling remain a niche but unchanged practice** for safety-critical systems; no superseding framework has displaced model checking plus cognitively plausible user models in 2024-2026.
