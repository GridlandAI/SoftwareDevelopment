# Testing Strategy and Practices

**Domain:** 13 - Quality, Testing, and Verification
**Primary sources:** Fifty Quick Ideas to Improve Your Tests (Adzic, Evans & Roden, 2015); Mastering Design Patterns for Layered Testing (Saini, 2025); Rigorous Software Development (Almeida, Frade, Pinto & Melo de Sousa, 2026)

## Overview
Testing is how a delivery team builds and sustains confidence that software does what it should and keeps doing so as it changes. A good strategy answers three questions at once: what to test, at which level, and how to keep the resulting test suite cheap to run and cheap to maintain. The hard part is not writing a single test, it is shaping a whole body of tests that gives fast feedback, survives frequent change, and still says something meaningful about quality.

This topic covers the testing levels and how to distribute tests across them (the test pyramid and its layered-testing refinement), how to design good checks and choose examples, how to keep large suites healthy over time, and how exploratory testing complements automated checking. It draws the strategic and example-design ideas largely from Adzic, Evans and Roden, the layered-testing and pyramid mechanics from Saini, and notes throughout where formal verification (Almeida) offers guarantees that testing structurally cannot.

## Core concepts

**Checking versus testing.** A useful distinction running through Adzic, Evans and Roden: a *check* is a deterministic, repeatable evaluation of a known expectation (ideal for automation), while *testing* (especially exploratory testing) is an open-ended investigation that deals with the unexpected and the unknown. Automated regression suites are mostly checks. Treating exploratory investigation as if it were just unautomated checking is a category error.

**The fundamental limit of testing.** Almeida frames testing precisely: it consists of producing data believed to be representative, feeding it to the system, and comparing actual to expected outputs. Because the input space is usually immense or infinite, exhaustive testing is impractical or impossible. Testing yields *confidence*, not proof. Coverage (the share of the system's control-flow exercised) is the usual proxy for that confidence. This is the structural reason formal methods exist: they reason over the *complete* data space rather than samples.

**Testing levels.** From Saini:
- **Unit tests** verify individual components or functions in isolation. Small, fast, developer-owned, run on every change, give immediate feedback. They form the base of the pyramid.
- **Integration tests** verify interactions between components or modules (data flow, API communication, integration points). Broader scope, more setup/teardown, slower than unit tests.
- **System / end-to-end (E2E) tests** exercise the whole application as a user would, validating that front-end and back-end work together across a real journey. Most comprehensive but slowest, most brittle, and most expensive to maintain - use sparingly on critical journeys only.
- **Acceptance tests** validate behavior against business expectations and acceptance criteria; in iterative delivery the acceptance criteria often double as the specification (Adzic et al.).

**What to test, and what not to test.** Adzic, Evans and Roden push teams to test *capabilities and benefits*, not just features and implementation, and to be deliberate about coverage rather than exhaustive. Saini's "Identifying Scope and Coverage" makes the same point at the strategic level: define the boundaries of what will and will not be tested based on risk, impact and priority, so critical components are well covered and low-risk areas are not over-tested.

## Frameworks & models

**The test pyramid (Mike Cohn, via Saini).** The ideal distribution of tests: a large base of fast automated unit tests, fewer integration tests in the middle, and an even smaller cap of E2E tests. The pyramid balances coverage, execution speed, and maintenance effort. Its inverse, the "ice-cream cone" (heavy UI automation, thin unit base), is the classic anti-pattern: slow, brittle, expensive.

**Layered testing (Saini).** An enhanced reading of the pyramid that treats the application's layers (UI, API/service, backend/business logic) explicitly and asks, for each test case, which layer is the most efficient and least brittle place to run it. Business logic should be validated at the component or API level rather than driven through the UI. Saini frames several *sequencing and allocation* strategies for building layered coverage (detailed as patterns below): Foundation-First, Layer-Specific Logic, Balanced Layer Distribution, Simplicity-Driven, Risk-Based, and Feedback-Focused.

**A multi-layered view of quality (Adzic et al., "Define a shared big-picture view of quality").** Borrowing from Maslow's hierarchy, frame quality as a pyramid the whole team agrees on:
1. Does it work at all? (key features, key technical qualities)
2. Does it work well? (performance, security, scalability)
3. Is it usable? (key usability scenarios)
4. Is it useful? (production metrics showing real use)
5. Is it successful? (business metrics, financial constraints)
Building this together with developers, testers, analysts and business stakeholders aligns expectations, prevents over-investing in already-de-risked levels, and clarifies what counts as a critical defect.

**Three design forces to balance (Adzic et al., "Balance three competing forces").** Every check is pulled between being a good *specification* (readable, illustrative), a good *acceptance test* (precise, complete enough to sign off), and a good *regression test* (cheap to run and maintain). No single test optimizes all three; suite design is about choosing which force dominates for each test.

**Functional, safety, and security requirements (Almeida).** Requirements split into functional (what the system does, input/output relation) and non-functional. Two specific classes deserve named attention: *safety* (nothing bad happens - e.g. memory safety, no illegal accesses) and *security* (access control to resources). Testing strategy should explicitly cover non-functional requirements (performance, security, usability), a gap Saini lists as a common flaw.

## Patterns
*(Patterns also recorded in PATTERN-CATALOG.md under Domain 13.)*

- **Shared quality pyramid** - Intent: create a multi-layered, cross-functional definition of quality so groups stop arguing past each other. When to use: once per milestone, in a room with senior reps of each discipline. Trade-offs: high-level by design (do not try to capture every test case); needs facilitation and quantification of vague terms. *(Adzic et al.)*
- **Explore capabilities, not features** - Intent: base exploratory sessions on user capabilities rather than the changed feature, to escape tunnel vision and find unexpected risks. When to use: exploratory testing of completed stories. Trade-offs: wider net needs a focusing frame (capabilities) or it loses direction; features remain the right basis for deterministic checks. *(Adzic et al.)*
- **Focus on key examples** - Intent: illustrate a story with a small number of simple, well-chosen scenarios instead of an exhaustive scenario dump. When to use: writing acceptance criteria / executable specs. Trade-offs: demands finding the right abstraction and mental model; resist the false sense of completeness a long scenario list gives. *(Adzic et al.)*
- **Contrast examples with counter-examples** - Intent: pair each positive example with a counter-example to sharpen the boundary of a rule. When to use: defining acceptance criteria. Trade-offs: more examples up front, but exposes hidden assumptions. *(Adzic et al.)*
- **Describe what, not how** - Intent: write checks in terms of business intent, not UI mechanics or implementation steps, so they survive refactoring. When to use: acceptance/specification checks. Trade-offs: needs a domain vocabulary; tests become more abstract. *(Adzic et al.)*
- **Given-When-Then in strict sequence** - Intent: one context, one action, one set of outcomes, in that order, with no interleaving. When to use: any scenario-based check. Trade-offs: forces single-topic tests; multi-action workflows must be modeled differently. *(Adzic et al.)*
- **One test, one topic** - Intent: each test exercises exactly one rule or behavior so failures localize. When to use: always. Trade-offs: more, smaller tests; clearer diagnosis. *(Adzic et al.)*
- **Write assertions first** - Intent: state the expected outcome before the inputs, to clarify the test's purpose and avoid backfilling expectations. When to use: designing checks/specs. Trade-offs: a habit shift; prevents confirmation-shaped tests. *(Adzic et al.)*
- **Split technical and business checks** - Intent: keep low-level technical checks separate from business-facing acceptance checks. When to use: mixed suites. Trade-offs: two categories to maintain; each stays readable to its audience. *(Adzic et al.)*
- **Decouple coverage from purpose** - Intent: measure code coverage to find untested code, but never confuse it with the purpose or value of a test. When to use: reasoning about suite health. Trade-offs: needs judgment; prevents coverage theater. *(Adzic et al.)*
- **Split just-in-case tests from key examples** - Intent: separate the small set of illustrative, must-pass examples from the larger body of defensive/edge tests. When to use: organizing large suites. Trade-offs: extra structure; key examples stay readable as living documentation. *(Adzic et al.)*
- **Optimize for reading, not writing** - Intent: invest in test readability (names, intros, structure) because tests are read far more often than written. When to use: every test in a long-lived suite. Trade-offs: more authoring effort; large maintenance payoff. *(Adzic et al.)*

## Principles & best practices

- **Confidence, not proof.** Be explicit that a green suite means "no known representative input fails," not "correct." Where correctness must be guaranteed (safety-critical paths, security invariants), reach for formal verification, not more tests (Almeida).
- **Push tests down the pyramid.** Validate logic at the lowest layer where it lives; reserve E2E for a few critical journeys (Saini).
- **Make developers responsible for checking.** Automated checks are part of the definition of done, not a separate downstream activity (Adzic et al., "Make developers responsible for checking").
- **Design tests together with other teams / mob your test sessions.** Cross-functional design of examples surfaces differing interpretations before code is written (Adzic et al.).
- **Wait for events, not time.** In asynchronous tests, synchronize on observable events rather than sleeps, to kill flakiness (Adzic et al., "Wait for events, not time").
- **Set up before async tests; do not clean up after.** Leaving data in place aids debugging and avoids teardown races (Adzic et al.).
- **Minimize UI interactions; separate decisions, workflows and technical interactions.** Decompose UI-driven tests so the decision logic is checked below the UI and only thin workflow remains at the top (Adzic et al.).
- **Version-control tests with the software; avoid organizing tests by work item.** Organize by capability/behavior so tests stay findable as stories come and go (Adzic et al.).
- **Avoid strict coverage targets; name tests for searchability; explain a test's purpose in its introduction.** Suite hygiene that keeps large bodies of tests navigable and meaningful (Adzic et al.).
- **Test non-functional requirements deliberately.** Performance, security, and usability need their own checks, not just functional happy paths (Saini).
- **Manage test data consistently.** Ad-hoc test data is a leading cause of flaky tests; use a structured, reusable strategy (Saini).

## Anti-patterns & pitfalls

- **Exhaustive scenario dumps.** Pages of scenarios give a false sense of completeness, kill discussion, prevent work-splitting, and signal that the technical model is misaligned with the business model (Adzic et al., "Focus on key examples").
- **Story-focused exploratory testing.** Exploring only the changed feature creates tunnel vision and proves what you already expect (Adzic et al.).
- **Heavy UI automation (the ice-cream cone).** Slow, brittle, high-maintenance; the most common flaw Saini names in automating functional tests.
- **Automating manual tests verbatim.** Manual and automated tests have different designs; transcribing one into the other produces poor checks (Adzic et al., "Don't automate manual tests").
- **Treating coverage as a goal.** Strict coverage targets and coverage-as-purpose incentivize meaningless tests (Adzic et al.).
- **Neglecting test maintenance.** Suites rot; obsolete tests produce false positives and erode trust unless reviewed and refactored (Saini).
- **Building frameworks that cannot scale.** Ignoring parallelism, large datasets, and distributed execution caps the suite's future (Saini).
- **Mistaking confidence for correctness.** Assuming 100% coverage equals correctness; coverage measures structure exercised, not behavior guaranteed (Almeida).

## Decision guidance

- **Choosing a level for a test:** put it at the lowest layer where the logic under test actually lives. Reserve integration tests for genuine cross-component interactions and E2E tests for a handful of business-critical journeys.
- **Deciding how much to test:** rank by risk, impact, and priority (Saini); use the shared quality pyramid to see which levels are already de-risked and stop there (Adzic et al.).
- **Choosing examples:** prefer a few key examples plus counter-examples over completeness; if you cannot keep the example set small and clear, treat that as a modelling smell, not a documentation problem (Adzic et al.).
- **When tests are not enough:** if a property must hold for *all* inputs (memory safety, a security/access invariant, a critical arithmetic computation), testing cannot establish it - escalate to deductive verification, model checking, or design-by-contract (Almeida; see formal-methods-and-verification.md).
- **Exploratory vs. automated:** automate deterministic, repeatable checks; spend human exploratory effort on the unknown and the unexpected, framed around capabilities and emotions/risks rather than features.

## Source attribution

- **Testing levels, the test pyramid, layered testing, automation flaws, test data and CI integration:** Saini (2025), Chapters 1-2 and the layered-distribution chapters.
- **Quality-as-pyramid, capabilities vs. features, key examples, the three competing forces, suite-management and readability ideas, exploratory-testing framing, checking vs. testing:** Adzic, Evans & Roden (2015), the four parts (Generating test ideas, Designing good checks, Improving testability, Managing large test suites).
- **The structural limits of testing, coverage as confidence, functional/safety/security requirements, and where verification supersedes testing:** Almeida et al. (2026), Chapter 1.

## Recent developments (2024-2026)

- **AI in testing is now mainstream.** By 2025 roughly 81% of development teams report using AI somewhere in their testing workflow, with software testing a primary GenAI use case; the AI-in-testing market is projected to grow from about USD 1.0B (2025) to USD 3.8B (2032) at ~20.9% CAGR. This makes AI-assisted checking part of the default strategy rather than a niche. (TestGuild, 2025-2026: https://testguild.com/7-innovative-ai-test-automation-tools-future-third-wave/ ; Talent500: https://talent500.com/blog/best-ai-test-automation-tools-2025-third-wave/)
- **Self-healing tests attack the maintenance problem directly.** AI now detects when a locator, layout, or DOM structure changes and automatically suggests or applies a working replacement, reducing flakiness and the manual upkeep that has historically made large UI suites expensive, a direct mitigation of the "neglecting test maintenance" and ice-cream-cone pitfalls in this file. (AvidClan, 2025: https://www.avidclan.com/blog/ai-powered-self-healing-test-automation-the-future-of-qa-in-2025/ ; Vocal Media: https://vocal.media/writers/the-rise-of-self-healing-test-automation-how-ai-is-changing-testing-in-2025)
- **AI-generated tests from requirements.** LLM-based tools parse a PRD or Jira ticket to extract acceptance criteria and draft test strategies and high-level scenarios, operationalizing the source's "acceptance criteria as specification" and "focus on key examples" ideas, though human review of the generated examples remains essential to avoid confirmation-shaped or low-value tests. (Monterail, 2025: https://www.monterail.com/blog/qa-with-ai-llm-powered-test-generation ; testRigor: https://testrigor.com/generative-ai-in-software-testing/)
- **The QA role is shifting from scriptwriter to AI supervisor.** Multiple 2025 sources describe QA moving from manual/repetitive execution toward guiding AI, validating its output, and doing more scenario design, risk analysis, and strategy, exactly the "checking vs. testing" and exploratory-investigation distinction this file draws, now amplified because deterministic checks are increasingly AI-generated and AI-maintained. (QASource, 2025: https://www.qasource.com/blog/qa-engineer-ai-testing-moves ; CloudQA: https://cloudqa.io/how-llms-are-reshaping-qa-in-2025/)
- **Shift-left and AI-accelerated release cycles.** Gartner-cited expectations are that around 70% of enterprises adopting AI-powered testing by 2026 will accelerate release cycles and improve quality; shift-left is treated as a non-negotiable DevOps strategy. (Techment, 2025: https://www.techment.com/blogs/ai-in-software-testing-2025-key-trends/)
- **Strategy fundamentals hold.** The test pyramid, layered testing, the confidence-not-proof limit, the shared quality pyramid, and pushing logic checks to the lowest layer remain sound; the 2024-2026 change is who/what writes and maintains the checks (AI agents) and the corresponding shift of human effort toward supervision, exploratory testing, and quality strategy.
