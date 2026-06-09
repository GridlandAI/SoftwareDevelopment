# Risk-Driven Architecture

**Domain:** 04 - Software Architecture Fundamentals
**Primary sources:** Just Enough Software Architecture: A Risk-Driven Approach (Fairbanks, 2010); Designing Software Architectures, 2nd ed. (Cervantes & Kazman, 2024); Hands-on Software Architecture (Kim & Kim, 2025)

## Overview
The hardest practical question in software architecture is not "what is the right design?" but "how much architecture work should I do, and which techniques are worth the time?" Every modeling or analysis technique consumes time that could be spent building the system. The risk-driven model (Fairbanks) answers this by promoting risk to prominence: do *just enough* architecture to reduce your most pressing risks, then resume coding. It reframes architecture as an engineering activity whose effort should be commensurate with the risk of failure - neither gold-plating with unneeded diagrams nor ignoring project-threatening quality-attribute risks.

This file covers the risk-driven model and its three steps, the spectrum of architecture engagement (indifferent / focused / hoisting), the conceptual model of software architecture (domain/design/code models, views, viewtypes, designation and refinement), the architecture-versus-detailed-design distinction, architecturally significant requirements, and how models and abstraction amplify reasoning. It is the "how much and why" companion to the step-by-step methods file.

## Core concepts

- **Risk (engineering definition).** Risk = perceived probability of failure × perceived impact. Bundling uncertainty into the definition means a risk can exist even if no actual fault does; applying techniques reduces uncertainty and therefore perceived risk. Describe risks as testable failure scenarios ("During peak loads, customers experience UI latencies greater than five seconds"), not vague categories ("reliability").
- **Engineering vs. project-management risk.** Engineering risks concern analysis, design, and implementation (the server may not scale to 1000 users); project-management risks concern schedule, staffing, geography ("lead developer hit by bus"). The technique type must match the risk type - engineering techniques will not solve management risks and vice versa. Risk is the shared vocabulary between engineers and managers.
- **Prototypical risks.** Risks common to a domain. Information/IT projects: complex/poorly-understood domain, wrong COTS, integration, modifiability. Systems projects: performance, reliability, size, security, concurrency, composition. Web projects: security, scalability, developer productivity. Knowing the norm matters; spotting where your project deviates from it matters more (the blind spots).
- **Technique.** A risk-reduction action, on a spectrum from pure analyses (calculating stresses, queuing theory, threat modeling) to pure solutions (tactics, patterns). The risk-driven model emphasizes the analysis end (layer/component/deployment models, performance/security/reliability analysis, leveraging styles).
- **Architecturally significant requirement.** A requirement (functional or quality) that influences the architecture. Architectural significance is determined by a *chain of intentionality*: a high-level intention of the designer that constrains some lower-level detail. Detail can be architectural even when microscopic (e.g., the Java Beans method-naming pattern); a decision is architectural if it has non-local consequences that matter to a driver.
- **Quality attribute.** An externally visible property (security, usability, latency, modifiability) that the architecture enables or inhibits. Architecture is (mostly) orthogonal to functionality - you can build the same function on different architectures, but some skeletons make qualities easy and others make them hard.
- **Constraint as guide rail.** Architecture is "the art of imposing just enough constraints" so the system has the qualities you want. Constraints embody judgment, promote conceptual integrity, reduce complexity, and make runtime behavior predictable. What a system does *not* do is as important as what it does.

## Frameworks & models

### The risk-driven model (Fairbanks)
Three steps, applied relentlessly: "What are my risks? What are the best techniques to reduce them? Is the risk mitigated so I can resume coding?"

1. **Identify and prioritize risks.** Start from the requirements, looking for things hard to achieve; use Quality Attribute Workshops or taxonomy-based questionnaires/checklists to elicit and prioritize failure scenarios. Prioritize on two dimensions: stakeholder priority and developer-perceived difficulty. Validate that time/money spent matches stakeholder priorities.
2. **Select and apply a set of techniques.** Choose the techniques that best reduce the prioritized risks - an "optimal basket" that kills two birds with one stone where possible. Account for opportunity cost: every technique does something valuable, but you want the one your project needs most.
3. **Evaluate risk reduction.** Decide subjectively whether each risk is *sufficiently* mitigated. The model frames the decision and produces an articulable argument ("We identified A, B, C as risks, B primary; applied X and Y; judged B mitigated, so we resumed coding"); it does not make the judgment call for you.

**Guiding principle:** architecture effort should be commensurate with the risk of failure. Resulting architecture models are deliberately *incomplete* - detailed where risks live (e.g., performance models with no security detail) and sketchy or absent elsewhere. You cannot, and should not try to, eliminate all engineering risk; doing so would crowd out project-management risk (the Wright brothers would have first flown in 1953, not 1903).

**Guidance for choosing techniques.**
- *Problems to find vs. problems to prove* (Polya): "find one scalable design" is a problem to find (testable); "the system always conforms to the API" or "no deadlock" is a problem to prove (tests cannot demonstrate it in all cases). Match the technique to the kind of problem.
- *Analytic vs. analogic models* (Jackson/Ackoff): analytic models support direct computation (vectors, state machines, equations); analogic models require human/domain reasoning (a UML class diagram you confirm with experts). Using UML does not guarantee an analytic model.
- *Viewtype matching:* reason about modifiability from the module viewtype, performance from the runtime viewtype, security from deployment and module viewtypes.
- *Techniques have affinities:* Rate Monotonic Analysis for reliability, threat modeling for security, queuing theory for performance.

### Three approaches to architecture (Fairbanks)
- **Architecture-indifferent design.** Pay little attention to architecture; the system may become a big ball of mud, drift to an emergent architecture, or default to a presumptive architecture. Acceptable only for unchallenging systems - and you should never choose it through ignorance.
- **Architecture-focused design.** Deliberately choose an architecture compatible with your goals. Provides opportunities, not guarantees (the seatbelt you must remember to buckle).
- **Architecture hoisting.** A kind of focused design where the architecture *guarantees* a goal or property, so developers need write no further code to achieve it (the airbag that deploys automatically). Use for properties too important to leave to per-developer discipline.

### Conceptual model of software architecture (Fairbanks)
A conceptual model is the architect's "80 IQ points" - it lets you see software the way a coach sees a game. The canonical model structure has three primary models, abstract to concrete:
- **Domain model.** Enduring truths about the world relevant to the system, not under your control (billing cycles are 30 days). Types, relationships, behaviors.
- **Design model.** The system you will build, largely under your control - a *partial* set of design commitments. Composed of recursively nested **boundary models** (publicly visible interface only) and **internals models** (internal design too). Contains invariants, constraints, and styles that programming languages cannot express.
- **Code model.** The source code or an equivalent model - a *complete* (executable) set of design commitments.

**Relationships between models.** *Designation* maps similar things across models so they correspond (domain Advertisement type → design Advertisement type). The design need not be 100% consistent with the domain (it may simplify or restrict domain types), but broken correspondence breeds bugs. *Refinement* relates a low-detail and a high-detail model of the same thing (boundary↔internals, design↔code). Structural elements refine cleanly (a design module → code packages), but invariants/constraints/styles are present in the design model and absent in the code model - the design-code gap.

**Views and viewtypes.** Each primary model is like a comprehensive database, too large to work on directly; a **view** selects a subset of details. Views group into three primary **viewtypes**: the **module viewtype** (static artifacts: source, classes), the **runtime viewtype** (objects, processes), and the **allocation viewtype** (server rooms, hardware). Build only the views your risks require - a project with only scalability risk builds a narrow scalability model.

### Architecture vs. detailed design
Design = the decisions and intentions in developers' heads, partitioned into architecture (macroscopic, sweeping structure per Shaw & Garlan) and detailed design (everything else). The boundary is genuinely fuzzy; the most useful test is intentionality plus non-local consequence: a detail is architectural if a high-level intention reaches down to constrain it and it affects an overall quality of the system. This aligns with Cervantes & Kazman ("a decision is architectural if it has non-local consequences that matter to an architectural driver") and with their preference to drop the imprecise terms "high-level" and "detailed" design in favor of architectural / element-interaction / element-internals design.

## Patterns
**Presumptive architecture** - the family of architectures dominant in a domain (N-tier for IT, cooperating processes for operating systems, peer-to-peer for VOIP). *Intent:* a de facto default that matches the domain's common risks. *When to use:* when your project resembles the domain norm; developers using it "will almost always do fine," and the real decisions become COTS choices per tier. *Trade-offs:* incurious teams may adopt it without noticing where their project deviates from the norm (the slow-reboot risk that is minor for IT but major for a hospital). Related to but distinct from a **reference architecture**, which is a written, often aspirational specification rather than a de facto standard. (See PATTERN-CATALOG.md.)

## Principles & best practices
- Promote risk to prominence: be able to answer "what are your primary failure risks and the corresponding techniques?" as easily as "what features are you working on?"
- Make risks testable failure scenarios so you can later tell whether a technique mitigated them.
- Keep architecture effort commensurate with risk; accept incomplete, uneven models.
- Build a narrow model focused on the risk at hand rather than comprehensive documentation by default.
- Constrain deliberately - use constraints as guide rails to guarantee qualities and conceptual integrity.
- Choose your approach consciously: indifferent only when truly unchallenging; hoist properties too critical to leave to discipline.
- The risk-driven model is compatible with planned, evolutionary, and minimal-planned design and with waterfall, iterative, spiral, UP/RUP, and XP - it dictates *when* and *how much* design, not the process.

## Anti-patterns & pitfalls
- **Big ball of mud** - the most common architecture, the result of architecture-indifferent design scaled up without new abstractions or partitions.
- **Feature-only focus** - teams so focused on delivering features that they defer quality-attribute concerns to maintenance, where they are far costlier.
- **Process/template mismatch** - applying a fixed company template (always build a module dependency diagram, always spend 10% on architecture) regardless of the project's actual risks; front-end and back-end teams producing the same artifacts though their risks differ.
- **Trying to eliminate engineering risk** - over-applying analysis until the schedule (a project-management risk) is blown.
- **Mistaking notation for analysis** - assuming a UML diagram is analytic when it lacks the attributes needed to compute the answer.
- **Big Design Up Front (BDUF)** and **top-down-only design** - Fairbanks cautions against both; early coding often surfaces the hardest problems.

## Decision guidance
- **Deciding how much architecture to do.** Look at how bad it would be to get the architecture wrong; benefit is proportional to the architecture's contribution to overall risk (Amdahl's-law analogy). Architecture deserves more attention when: the solution space is small (human-powered airplane), failure risk is high (hospital software), quality attributes are difficult (millions of users), the domain is new to you, or it is a product-line architecture.
- **Deciding which technique.** Match technique to risk type (engineering vs. management), to problem kind (find vs. prove), to model kind (analytic vs. analogic), and to viewtype (modifiability→module, performance→runtime, security→deployment+module). Prefer techniques with affinities to the risk. Optimize the basket against opportunity cost.
- **Deciding the design style.** Teams with strong refactoring, test-driven development, and continuous integration can lean more on evolutionary design; do up-front planned design where you must ensure global/emergent qualities, avoid design dead-ends, or coordinate parallel sub-teams (set throughput budgets, concurrency policy, standard connectors). Minimal planned design (≈20% planned / 80% evolutionary, per Fowler) is a common balance.
- **Deciding the engagement approach.** Use architecture hoisting for safety-critical or non-negotiable properties; architecture-focused design when you can rely on conventions; never default to indifference unwittingly.
- **Relationship to the step-by-step methods.** Use the risk-driven model to decide scope and stopping criteria, then run ADD or UAP iterations against the highest-priority risks/drivers; both Cervantes & Kazman and Kim & Kim "let risk be the guide" for whether more iterations or evaluations are needed.

## Source attribution
- The risk-driven model and its three steps, engineering vs. management risk, prototypical risks, techniques spectrum, problems-to-find/prove, analytic/analogic models, viewtype matching, when-to-stop, planned/evolutionary/minimal design, indifferent/focused/hoisting, presumptive architectures, the conceptual model (domain/design/code, designation, refinement, views, viewtypes), architecture-vs-detailed-design, architecturally significant detail via intentionality: Just Enough Software Architecture, chapters 1–3 and 7 (Fairbanks, 2010).
- "Architectural if non-local consequences matter to a driver," prioritizing scenarios by importance × risk, using risk to decide when to stop iterating: Designing Software Architectures, 2nd ed., chapters 2–4 (Cervantes & Kazman, 2024).
- Risk-themed architecture evaluation and PoC/prototype-based feasibility evaluation as risk mitigation: Hands-on Software Architecture, chapters 11 and 13 (Kim & Kim, 2025).

## Recent developments (2024–2026)

- **A new "uncertainty-native" school complements risk-driven thinking.** Barry O'Reilly's Residuality Theory (*Residues*, 2024; widely taught through 2025-2026) extends the risk-driven instinct in a complexity-science direction: instead of enumerating and prioritizing known failure scenarios, it randomly simulates *stressors* from the business context to find hidden coupling and design for survival under unknown change. It reframes the goal as *criticality* (surviving unknown stress) rather than mitigating a fixed risk list, a useful counterpart to Fairbanks's "reduce your most pressing known risks." (InfoQ, "Producing a Better Software Architecture with Residuality Theory," 2025, https://www.infoq.com/news/2025/10/architectures-residuality-theory/)

- **AI raises the value of risk-driven discipline.** With probabilistic, AI-generated code and designs becoming common (the "AI 90/10 problem"), 2025-2026 commentary stresses that architects must concentrate effort on verifying and validating the genuinely risky parts rather than treating AI output as uniformly trustworthy, which is the risk-driven model applied to AI: spend technique where the failure probability and impact are highest. (SSRN, "Mind the Skills Gap: Reimagining the Software Architect's Role in the LLM Era," 2025, https://papers.ssrn.com/sol3/papers.cfm?abstract_id=5658241)

- **AI-assisted risk and feasibility evaluation.** Generative-AI support for ATAM-style scenario evaluation and prototyping (2025 research) lowers the cost of the "select and apply techniques / evaluate risk reduction" steps, letting teams run cheap, continuous risk checks instead of one heavyweight evaluation. (arXiv, "Supporting architecture evaluation for ATAM scenarios with LLMs," 2025, https://arxiv.org/html/2506.00150v1)

- **Core model unchanged.** The three steps, engineering-vs-management risk, technique spectrum, indifferent/focused/hoisting approaches, and the conceptual model (domain/design/code, viewtypes) remain as Fairbanks defined them; the 2024-2026 development is the rise of residuality as a complementary uncertainty method and AI's effect on where risk-driven effort should be spent.
