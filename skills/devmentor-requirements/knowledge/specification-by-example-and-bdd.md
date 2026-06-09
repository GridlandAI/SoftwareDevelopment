# Specification by Example and BDD

**Domain:** 02 - Requirements and Specifications
**Primary sources:** Specification by Example (Adzic, 2011); Writing Great Specifications: Using Specification by Example and Gherkin (Nicieja, 2017)

## Overview
Specification by Example (SBE) is a set of process patterns by which successful teams build the *right* software and avoid rework. Its central insight is that natural-language requirements are ambiguous and context-dependent, so teams should instead illustrate requirements with concrete, agreed examples. Those examples become a single artifact that is simultaneously the specification, the acceptance test, and - once automated and kept current - the living documentation of the system. With SBE, functional requirements, specifications, and acceptance tests are the same thing.

Behavior-Driven Development (BDD) is the closely related, behavior-specification-centric expression of the same ideas. It centers on building shared understanding through collaboration and on capturing system behavior as scenarios, typically written in the Given-When-Then structure of the Gherkin language. Adzic distinguishes an acceptance-testing-centric model (ATDD), useful for teams with functional-quality problems, from a system-behavior-specification model (BDD), useful for explaining short- and mid-term delivery once things run smoothly; he treats neither as superior. SBE/BDD bridges the communication gap between business and technical stakeholders by expressing requirements in business-readable, automatable form.

## Core concepts

**Example.** A concrete instance of how the system should behave for specific inputs (for example, "a VIP customer with five books in the cart gets free delivery"). Examples are precise where prose is vague.

**Key examples.** The minimal set of examples that, taken together, define what the software must do. They are both the target for development and the objective criterion for "done." Teams pick one example per important set of cases plus examples that illustrate edge cases or areas of concern, rather than trying to enumerate every combination (that is the job of combinatorial regression testing, not specifications).

**Specification with examples.** A refined, self-explanatory document combining a short description of the rule with a table of key examples. It is at once a specification of work, an acceptance test, and a future regression test.

**Executable specification.** A specification with examples that has been connected to the system via an automation framework *without changing the specification text*. The automation pulls inputs from the spec and checks outputs against it. The document looks the same as it did on the whiteboard - comprehensible to all - but can now be run.

**Living documentation.** A frequently validated body of executable specifications, well organized and consistent, that serves as a reliable, authoritative description of what the system does and why - as trustworthy as the code but far easier to read. (Covered in depth in `living-documentation.md`.)

**Scenario (Gherkin).** "A concrete example that illustrates a business rule" (Nicieja). Written in the Given-When-Then template.

**Checks vs. tests.** Adzic adopts the context-driven distinction: *checks* are deterministic validations that can be automated (what SBE produces); *tests* such as exploratory or usability testing require human judgment and are not specifications. Automating the checks frees testers for the genuine testing.

## Frameworks & models

### The seven key process patterns of SBE (Adzic, 2011)

1. **Deriving scope from goals.** Do not let business users hand the team a pre-designed solution (a list of features). Start from a measurable business goal (for example, "increase repeat sales to existing customers by 50% over the next 12 months"), then collaborate to derive the scope - the cheapest, fastest solution that meets the goal. Business users own intent and value; the delivery team contributes solution design.

2. **Specifying collaboratively.** Do not rely on one person to get specifications right in isolation. Bring business users, developers, and testers together (in specification workshops or smaller "Three Amigos" conversations) so the whole team's knowledge is captured: developers know the infrastructure, testers know where issues hide. Collaboration also creates collective ownership.

3. **Illustrating using examples.** Replace ambiguous prose with concrete examples discovered together. The act of finding examples flushes out functional gaps, edge cases, and inconsistencies and produces shared understanding. If the system works for all key examples, it meets the agreed specification.

4. **Refining the specification.** First-draft examples carry too much incidental detail (UI clicks, surplus context). Refine them to be concise, precise, and focused on *what* the system does, not *how*. Refined examples become the acceptance criteria.

5. **Automating validation without changing specifications.** Automate so that the specification text is unchanged - the inputs and expected outputs in the document drive the automation directly. This avoids the "telephone game" where detail is lost between business spec and technical test, and keeps the spec accessible to business users. Tools that support this include Cucumber, FitNesse, Concordion, and SpecFlow.

6. **Validating frequently.** Run all executable specifications often (typically in an automated build). Frequent validation surfaces any divergence between system and spec immediately, gives the spec the same trustworthiness as the code, and provides free functional regression testing.

7. **Evolving a living documentation system.** The most successful teams keep specifications well organized, easy to find, consistent, and current as the domain and system change. This long-term documentation value, more than regression testing, is the deepest payoff of SBE.

### The SBE cycle (worked example, Adzic)
Business goal → derive scope (themes broken into deliverable stories) → for the story being implemented, identify key examples through collaboration → refine into a specification with examples (description + example table) → automate to make it executable → validate frequently → when the story is done, restructure and merge the specification into the documentation hierarchy → repeat. (Adzic's free-delivery example: "free delivery for five or more books" with VIP/regular, books/electronics permutations becomes a concise example table that doubles as the test.)

### Gherkin and the Given-When-Then template (Nicieja, 2017)
Gherkin is a business-readable, jargon-free domain-specific language for capturing conversations as executable specifications. Structure:

```gherkin
Feature: Free shipping
  Free delivery is offered to VIP customers buying five or more books.

  Scenario: VIP customer with enough books gets free shipping
    Given a VIP customer
    When the customer puts five books in the cart
    Then shipping should be free
```

- **Feature** begins the file and names the capability, with an optional free-text description.
- **Scenario** is one concrete example of a business rule.
- **Given** establishes context/preconditions, **When** is the event/action, **Then** is the expected outcome.
- **And / But** chain additional steps.
- **Background** holds Givens shared by every scenario in the file.
- **Scenario Outline** plus an **Examples** table parameterizes a scenario across many input rows (placeholders in `<angle brackets>`); the test runner generates one scenario per example row before execution.

**Capturing the conversation.** A free-flowing specification-workshop conversation is unreliable long-term memory; Gherkin is the long-term storage that extracts the essential actions and removes noise, in plain English using the product's ubiquitous language.

### Example mapping (collaborative discovery)
Although Adzic frames discovery as specification workshops and Nicieja uses outcome/context questioning, both describe the same move: take a rule (or user story), enumerate concrete examples and counterexamples, and surface open questions. Nicieja's techniques - **starting with the Then** (begin from the outcome to get unstuck and to engage business users with tangible outputs), **outcome questioning** ("is there another outcome that also matters?"), and **context questioning** ("is there a context that, for the same event, gives a different outcome?") - drive out the key examples and their boundaries.

## Patterns

**Declarative (outcome-oriented) scenarios** - Intent: write scenarios about business intentions and outcomes, not UI mechanics. When to use: almost always; this is the default style for durable specifications. Trade-offs: more abstract and shorter (easier to read, discuss, remember; pushes "how" into step definitions and the automation layer); occasionally too terse to convey a hard domain concept in full. Liz Keogh's nuance: declarative vs. imperative is really a choice of abstraction level ("chunking up" vs. "chunking down").

**Imperative (interface-oriented) scenarios** - Intent: spell out concrete UI steps (click this button, fill that field). When to use: rarely, and mainly because newcomers default to it; acceptable only for genuinely UI-level checks. Trade-offs: intuitive and mirrors manual testing, but brittle - every UI change breaks the spec, eroding trust - and it documents *how something is tested* rather than *what the system does*.

**Counterexamples** - Intent: pair every positive example with a negative one to define the boundary of a rule (VIP with five books → free; VIP with four → standard; regular with five → standard). When to use: whenever specifying a rule with a threshold or condition. Trade-offs: doubles example count but makes the rule's edges unambiguous and prevents misimplementation.

**Scenario outline for redundancy** - Intent: collapse several near-identical scenarios that differ only in data into one outline driven by an Examples table. When to use: when you spot duplicated scenarios. Trade-offs: shorter, better-organized feature files; but outlines can hide complexity and become unreadable if overloaded - keep each table focused on a single business rule.

**Warm-up narrative (Nicieja)** - Intent: before writing scenarios, sketch a short "day in the life" narrative of an actor using the feature. When to use: to get unstuck and to surface new solution ideas. Trade-offs: throwaway prose, not a substitute for scenarios; can be kept as feature description if non-redundant.

**Tests are specifications; specifications are tests** - Intent: maintain one artifact that serves as requirement, acceptance test, and regression test. When to use: as the foundational SBE stance. Trade-offs: requires discipline to keep specs business-readable and automation separate; the payoff is a single source of truth with no synchronization drift.

## Principles & best practices

- **Start from goals, not solutions.** Derive scope; let the team design the cheapest path to a measurable goal (Adzic).
- **Specify with the whole team,** not a lone analyst - capture diverse expertise and create collective ownership (Adzic).
- **Illustrate with concrete examples;** they remove ambiguity and build shared understanding (Adzic).
- **Refine ruthlessly:** precise and testable, self-explanatory, focused on a single function, in domain language, and about business functionality - not scripts, not software design (Adzic, ch. 8).
- **Automate without distorting the specification;** the executable spec should read the same as the whiteboard (Adzic).
- **Validate frequently** so specs stay as trustworthy as the code (Adzic).
- **Use the ubiquitous (domain) language** consistently across specifications, design, and tests (Adzic; Nicieja).
- **Write declaratively about intentions and outcomes,** not about buttons and links (Nicieja).
- **Start scenarios with the Then;** discuss outputs first to engage business users (Nicieja).
- **Don't try to cover every case;** specifications are not a replacement for combinatorial regression testing (Adzic).
- **Keep a check/test split:** automate deterministic checks; reserve human judgment for exploratory and usability testing (Adzic).

## Anti-patterns & pitfalls

- **Accepting a pre-designed solution as requirements,** so the project does what the customer asked but not what they wanted (Adzic).
- **Specifying in isolation,** forcing post-delivery validation and rework (Adzic).
- **Scripts instead of specifications** - describing step-by-step *how* rather than *what*; a leading cause of brittle, unmaintainable feature files (Adzic, ch. 8; Nicieja).
- **Imperative, UI-coupled scenarios** that break on every interface change and read as test scripts (Nicieja).
- **Technical automation concepts leaking into scenarios** (DOM ids, SQL, "wait until") - makes specs unreadable to business and often signals a system-design problem (Adzic).
- **Over-long specifications** describing several functions at once, or **too many tiny specs** fragmenting one feature - both hurt understanding (Adzic).
- **Boomerangs (Nicieja)** - features that keep breaking and returning from production: caused by complex domain rules, missed stakeholders, bus-factor-of-one knowledge loss, or unsatisfied customers. Collecting examples from real users and capturing domain knowledge in executable specs reduces their likelihood and recurrence.
- **Trying to enumerate every combination as an example,** confusing specification with exhaustive testing (Adzic).

## Decision guidance

- **ATDD vs. BDD emphasis:** lean acceptance-testing-centric (ATDD) when the team has many functional-quality issues and needs clear targets and regression safety; lean behavior-specification-centric (BDD) when quality is stable and the goal is shared understanding and communication. They are the same underlying process (Adzic).
- **Choosing examples:** pick one per important equivalence class, add edge cases and areas of programmer/tester concern, always include counterexamples to mark boundaries; stop before combinatorial explosion (Adzic; Nicieja).
- **Declarative vs. imperative:** default to declarative/outcome-oriented at a business-rule abstraction level; drop to a more concrete level only when needed to convey a hard concept, and only use UI-level steps for genuine UI checks (Nicieja).
- **When to use a scenario outline:** when multiple scenarios differ only by data values; keep each Examples table to one rule.
- **Automation choice:** prefer tools that automate without altering the spec text (Cucumber/Gherkin, FitNesse, Concordion, SpecFlow); the book's guidance is tool-agnostic - the practice matters more than the tool.

## Source attribution
- The seven key process patterns (deriving scope from goals, specifying collaboratively, illustrating using examples, refining, automating without changing specs, validating frequently, evolving documentation), key examples, specification with examples, executable specification, the ATDD vs. BDD models, checks vs. tests, the worked free-delivery example, refinement qualities, and the script/long-spec/technical-concept anti-patterns: **Adzic, *Specification by Example*, 2011** (Chapters 1, 2, 3, 8, 9).
- Gherkin syntax (Feature/Scenario/Given-When-Then/Background/Scenario Outline/Examples), scenarios as captured conversations, declarative vs. imperative style and abstraction levels, starting with the Then, outcome/context questioning, counterexamples, scenario-outline refactoring, warm-up narratives, ubiquitous language in Gherkin, and boomerangs: **Nicieja, *Writing Great Specifications*, 2017** (Chapters 1, 2, 3, 4, 5).

## Recent developments (2024–2026)
SBE and BDD are mature and stable; Gherkin, Cucumber, and the seven process patterns are unchanged. The 2024-2026 developments are AI assistance for generating and maintaining scenarios, and a strong conceptual overlap with the new spec-driven development movement.

- **AI-generated Gherkin from user stories.** 2025 tooling can create Given-When-Then scenarios directly from user stories, suggest step definitions, identify coverage gaps by analyzing existing feature files, and propose new scenarios based on real user journeys, lowering the cost of Adzic's "illustrating using examples" pattern while keeping the business-readable form. The caveat mirrors the books: AI proposes, humans curate to keep scenarios declarative and aligned to business rules. (Cucumber docs, 2025; OmegaNext, 2025, news.omeganext.com)
- **AI for scenario maintenance and anti-flakiness.** AI is being applied to refine flaky steps, deduplicate step definitions, and auto-maintain scenarios as application behavior changes, addressing the brittleness and maintainability concerns Adzic and Nicieja warn about and supporting the "listen to your living documentation" stance. (OmegaNext, 2025; AccelQ, 2025)
- **Spec-driven development is SBE's ideas applied to AI code generation.** The 2025 SDD movement (GitHub Spec Kit, Amazon Kiro) restates SBE's core claim, that an executable, agreed specification is the single source of truth, but now the specification drives AI agents that generate and validate code, not just regression tests. "AI makes specifications executable" is essentially Adzic's executable-specification idea generalized beyond test automation. The risk SDD highlights (specs that are imprecise produce wrong code) reinforces Adzic's refinement discipline. (Martin Fowler, 2025; GitHub Blog, 2025)
- **Declarative, intent-focused scenarios matter more, not less.** As AI consumes specifications, the long-standing guidance to write declaratively about outcomes (Nicieja) and to keep technical/UI mechanics out of scenario text pays off directly: intent-level specs are better prompts and remain stable across implementation changes, while imperative UI-coupled scenarios break and mislead generators. (Cucumber "Writing better Gherkin," 2025)
- **Living documentation gains an AI-native rationale.** Keeping specifications current and business-readable now also serves AI agents and onboarding assistants that read the suite as context, adding a fresh incentive to avoid Nicieja's "speci-fiction" (outdated docs masquerading as truth). (industry discussion, 2025)
