# Living Documentation

**Domain:** 02 - Requirements and Specifications
**Primary sources:** Specification by Example (Adzic, 2011), Chapters 3, 10, 11; Writing Great Specifications (Nicieja, 2017), Chapters 1, 7, 8

## Overview
Living documentation is the long-term payoff of Specification by Example: a body of executable specifications that is kept organized, consistent, and frequently validated so that it remains a reliable, authoritative description of what a system does and why. Unlike conventional documentation - which, like cheap wine, ages rapidly and is abandoned because nobody updates it - living documentation is connected to the code through automated tests, so any divergence between document and system shows up immediately as a failing check. It is "as reliable as the code but much easier to read and understand."

Adzic argues this documentation value, not regression testing, is the deepest benefit of SBE: regression testing alone (with an average defect-removal efficiency cited at only ~23%) cannot justify the investment teams make. The living documentation system lets support staff learn what the system does, lets developers use it as a development target, lets testers use it for testing, and lets business analysts use it to analyze the impact of proposed changes - while providing free regression testing as a side effect. Nicieja frames the opposite as "speci-fiction": a specification that poses as a single source of truth but cannot be one because nobody updates it.

## Core concepts

**Living documentation (Nicieja's definition).** "Documentation that changes along with the system it documents, thanks to the link between the text and the code as well as frequent validation." The link (via tests) forces the documentation to change whenever the underlying code changes.

**Speci-fiction (Nicieja).** A specification that claims to be the single source of truth but is outdated because no one maintains it. It gives only an illusion of correctness and actively misleads readers. It typically results from many last-minute changes during a release frenzy that never make it back into the document.

**System archeology (Adzic, via Christian Hassa).** The painful process of reverse-engineering business functionality from source code when no trustworthy documentation exists. Living documentation exists to make this unnecessary - and reverse engineering is impossible for business users, testers, and support engineers anyway.

**Business-process documentation.** Adzic frames the end goal not as test artifacts but as documentation of how the business works. Living documentation is the longest-living artifact of a project: technologies and code come and go, but the documentation of business behavior persists for months or years.

**Specification suite (Nicieja).** The organized whole formed by many feature files / executable specifications, managed together as a single navigable documentation system.

## Frameworks & models

### The three quality goals of living documentation (Adzic, ch. 11)
A living documentation system must be:
1. **Easy to understand**
2. **Consistent**
3. **Organized for easy access**

The chapter's techniques map directly to these three goals.

### Easy to understand
- **Don't create long specifications.** A too-long specification signals a problem: concepts at the wrong abstraction level (look for the missing concept - this can yield design breakthroughs), several functions bundled into one spec (split them), a script rather than a specification (restructure to *what*, not *how*), or surplus contextual detail (strip it to the attributes that illustrate the goal).
- **Don't fragment one feature across many tiny specs.** If someone must read ten specifications to understand one feature, reorganize. Concepts that drift together over time should be merged.
- **Look for higher-level concepts.** When many specs differ only slightly, abstract them: replace a set of near-identical specs with one that varies only the differing attributes (often a scenario outline).
- **Avoid technical automation concepts in the spec text** (DOM ids, SQL, "wait until") when stakeholders are non-technical - they break readability and usually point to a system-design problem.

### Consistent
- **Evolve a specification language.** Nearly all of Adzic's research teams developed a set of reusable specification patterns / a mini-DSL. Consistent wording makes specs easier to read and far easier to automate (developers recognize the same structure). Inconsistency produces "57 ways to navigate to a page."
- **Keep language in sync as the domain evolves.** Old specs left in three-year-old jargon force translation and defeat the documentation's purpose. Continual refactoring of the language is low-cost and high-value.
- **Review for consistency.** Teams where business users write specs (for example, Bekk Consulting's dairy-herd system) have developers review anything structurally different to keep it consistent and automatable.

### Organized for easy access
Living documentation systems grow fast - hundreds of specs within months, tens of thousands of checks over years. Two complementary organizing structures (Adzic, ch. 10–11):
- **Organize current work by stories.** Group all specs for the in-progress story together (a "current iteration pack") so they execute as one test pack and give a clear definition of done. A single story may touch several functional areas, each with its own focused spec.
- **Reorganize completed stories by functional area.** User stories are a planning tool, not a navigation tool. Six months on, "PayPal came in via story #128" is irrelevant. Once a story is done, move its specs into a functional hierarchy (payments, user management, etc.) so anyone can find a feature by navigating business functionality. Use cross-referencing tools if story traceability is still needed (for example, for regulatory reasons).

### Organizing the specification suite (Nicieja, ch. 8)
- **Organizing by features** - the default; group scenarios under the capability they describe.
- **Organizing by user stories** - using an "Ability" keyword and the user-story structure; useful for current work but carries "iterative dangers" (the same drift Adzic warns about - stories are transient, so a pure story organization rots over time).
- **Nonfunctional requirements** - handled per SBE where possible; Nicieja introduces a "Business Need" keyword to specify nonfunctional user stories and to identify the stakeholders behind them.

### Who creates and maintains living documentation
A shared responsibility: business users contribute intent and examples; developers and testers contribute automation and review for consistency. Some teams have dedicated people make requirements readable for everyone; everyone involved must be able to read the results. Tools such as Relish, Cucumber Pro, and CukeHub publish scenarios from a code repository so the whole team can browse and collaborate like a shared document.

## Patterns

**Specifications-as-documentation** - Intent: keep one set of artifacts that is requirement, test, and documentation simultaneously, so there is never more than one version of the truth. When to use: as the core stance of any SBE/BDD adoption. Trade-offs: demands discipline to keep specs business-readable and continually refactored; the reward is no synchronization drift and trustworthy docs.

**Current-iteration pack** - Intent: group all in-progress story specs in one hierarchy branch for fast execution and a clear "done." When to use: during active development of a story. Trade-offs: temporary structure; the specs must be relocated once the story ships.

**Functional-area reorganization** - Intent: after a story is delivered, move its specs into a business-functionality hierarchy. When to use: at story completion. Trade-offs: ongoing curation cost; pays back every time someone needs to understand or change a feature. Optionally cross-reference back to the originating story.

**Living dictionary / documented building blocks** - Intent: maintain a documentation area of reusable specification components (personas, common steps), ideally generated from the automation code so it is guaranteed accurate (for example, Iowa Student Loan's persona page). When to use: as the suite and its shared language grow. Trade-offs: small upkeep; greatly eases onboarding and keeps language consistent. A lightweight alternative is to point newcomers at good representative existing specs (which, being executable, are guaranteed current).

**Listen to your living documentation (Adzic)** - Intent: treat friction in the docs as a design signal. When a spec needs technical concepts, grows too long, or is hard to keep consistent, the underlying system or model is probably wrong (for example, unreliable asynchronous processes, a wrong domain model, missing concepts). When to use: continuously, during refinement and maintenance. Trade-offs: requires the team to act on the signal rather than paper over it; rewards with better system design.

## Principles & best practices
- **Validate frequently** (ideally in an automated build) so the documentation cannot silently fall out of sync (Adzic).
- **Refine relentlessly:** focused, self-explanatory, in domain language, about business functionality - never scripts (Adzic, ch. 8).
- **Think about high-level structure early.** Phil Cowans' lesson: organizing the suite along the product's feature set from the start (not "the last thing we built") prevents the suite from becoming as opaque as the code (Adzic).
- **Use two structures:** stories for current work, functional areas for completed work (Adzic).
- **Evolve and refactor a consistent specification language;** review structurally divergent specs (Adzic).
- **Keep specs business-readable and free of technical automation detail** when stakeholders are non-technical (Adzic; Nicieja).
- **Make it everyone's artifact;** publish it where the whole team can read it (Adzic; Nicieja).
- **Capture conversations, not just tests:** every scenario should trace back to the conversation that produced it, so the rationale survives (Nicieja).

## Anti-patterns & pitfalls
- **Speci-fiction** - outdated docs masquerading as the source of truth (Nicieja). The core failure living documentation is designed to prevent.
- **Acceptance-test-centric neglect of maintainability.** Teams that focus only on tests write tests that are hard to update; without designing specs to reveal purpose and be easy to modify, the suite rots (Adzic).
- **Technical/cryptic tests** that only developers can read (Johannes Link's FIT experience) - they take longer to run and maintain and fail as a communication tool (Adzic).
- **Scripts in place of specifications** - describing *how* it is tested rather than *what* it does; brittle and unmaintainable (Adzic; Nicieja).
- **Organizing the whole suite by user story** - stories are transient; six months later nobody remembers story numbers, and the suite becomes unnavigable (Adzic; Nicieja's "iterative dangers").
- **No high-level structure** - adding tests ad hoc until no one can find which tests cover which functionality (Adzic).
- **Inconsistent, drifting language** across specs written at different times - forces translation and erodes value (Adzic).
- **Letting specifications grow too long or fragmenting a feature across too many tiny specs** - both defeat understanding (Adzic).

## Decision guidance
- **Is it living documentation or speci-fiction?** If specs are not connected to code and validated frequently, they are speci-fiction regardless of how thorough they look. Frequent automated validation is the dividing line.
- **How to organize:** keep current work grouped by story (for execution and definition of done); migrate finished work into a functional-area hierarchy (for long-term navigation). Cross-reference to stories only if traceability is genuinely required.
- **When a spec is hard to write or maintain, change the system, not just the spec.** "Listen to the documentation": technical leakage, excessive length, or persistent inconsistency are signals of design or model problems.
- **Readability vs. precision:** choose the abstraction level that lets a non-technical stakeholder understand the rule; push mechanics into the automation layer.
- **Onboarding:** point new members at good existing specs and a generated living dictionary of building blocks rather than at separate prose documentation.
- **Tooling:** any framework that automates without altering spec text works (Cucumber, FitNesse, Concordion, SpecFlow); add a publishing layer (Relish, Cucumber Pro, CukeHub) so non-developers can read the suite.

## Source attribution
- Why authoritative documentation is needed, system archeology, the ATDD-vs-documentation perspective, the three quality goals (easy to understand, consistent, organized for easy access) and all their techniques, organizing current work by stories vs. reorganizing by functional area, the current-iteration pack, documented building blocks / living dictionary, evolving a consistent specification language, and "listen to your living documentation": **Adzic, *Specification by Example*, 2011** (Chapters 3, 10, 11).
- The definition of living documentation, the link-to-code-via-tests mechanism, speci-fiction, capturing conversations so they trace back to their origin, organizing scenarios into a specification suite (by feature vs. by user story, iterative dangers), and the Business Need keyword for nonfunctional requirements: **Nicieja, *Writing Great Specifications*, 2017** (Chapters 1, 7, 8).

## Recent developments (2024–2026)
The living-documentation concept (executable specs linked to code, validated frequently, organized for access, kept business-readable) is stable. The 2024-2026 developments give it a powerful new consumer and a new maintenance assistant: AI.

- **AI agents are now a primary reader of living documentation.** As agentic and AI-assisted development spreads, current, business-readable, code-linked specifications become high-value context for AI coding and support assistants, adding a fresh, concrete reason to avoid Nicieja's "speci-fiction." Documentation that drifts from code now degrades not only human understanding but also the quality of AI output. (industry discussion, 2025; Cucumber docs, 2025)
- **AI-assisted maintenance reduces the upkeep cost living documentation depends on.** 2025 tooling can auto-maintain scenarios as behavior changes, refactor duplicated steps, detect coverage gaps, and propose new examples, lowering the curation burden that Adzic identifies as the make-or-break factor for keeping a suite trustworthy. The "listen to your living documentation" signal (friction means a design problem) still applies, but AI can surface those signals earlier. (OmegaNext, 2025; AccelQ, 2025)
- **Spec-driven development is living documentation generalized.** GitHub Spec Kit and Amazon Kiro keep a small set of Markdown specification artifacts (requirements, design, tasks) as the maintained source of truth an AI agent builds against, an architecture that depends on exactly the qualities this topic prescribes: easy to understand, consistent, and organized. The SDD risk (stale or vague specs yield wrong code) is the speci-fiction failure mode with higher stakes. (GitHub Blog, 2025; Kiro, 2025; Martin Fowler, 2025)
- **Generated docs and "docs as code" continue to converge with executable specs.** The trend toward generating reference material from code and tests (so it cannot fall out of sync) aligns with Adzic's living-dictionary pattern; AI now helps generate and summarize these building blocks, though the discipline of frequent validation remains the dividing line between living documentation and decoration. (industry tooling, 2024-2025)
- **Readability for mixed human/AI audiences raises the value of ubiquitous language.** Keeping one consistent domain language across specs (Adzic's "evolve a specification language") helps both human stakeholders and AI tools resolve intent, making consistency reviews and a maintained living dictionary more valuable as teams adopt AI. (industry discussion, 2025)
