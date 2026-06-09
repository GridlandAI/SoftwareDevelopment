# Test Design Patterns

**Domain:** 13 - Quality, Testing, and Verification
**Primary sources:** Mastering Design Patterns for Layered Testing (Saini, 2025); Fifty Quick Ideas to Improve Your Tests (Adzic, Evans & Roden, 2015)

## Overview
Test design patterns are standardized, reusable solutions for structuring automated tests so they stay maintainable, scalable, and adaptable as the system under test evolves. Just as design patterns solve recurring coding problems, test design patterns address the recurring problems of automation: redundancy, fragility, slow feedback, and unmanageable maintenance. They operate at two scales: *micro* patterns that shape an individual test (structure, doubles, data), and *macro* patterns that shape how a whole suite is distributed across application layers and how the automation framework is architected.

This file captures Saini's layered-testing patterns, the page object and related UI patterns, the full taxonomy of test doubles (stub, spy, fake, mock), data builders and fixtures, and framework-design guidance, with cross-references to the example-design patterns owned by testing-strategy-and-practices.md.

## Core concepts

**Why patterns matter in automation.** Poorly structured tests become a burden quickly: hard to maintain, prone to breaking, and difficult to extend. Patterns make tests *modular, reusable, and understandable*, which is what lets a suite survive the application changing underneath it (Saini, Ch. 1).

**Manual vs. automation test design.** Manual testing is usually confined to the UI - screens, forms, buttons - because manual testers often lack access to the code. Automation expands the testable surface to APIs, databases, and individual functions, so automation design is fundamentally about *choosing the right layer* for each test rather than driving everything through the UI (Saini).

**Test doubles.** Generic term for any object that stands in for a real dependency during a test. Saini and the broader xUnit tradition distinguish them by behavior:
- **Stub** - returns predefined responses for given inputs; does not record or verify usage. Use to feed a unit fixed data (e.g., a query returning a static dataset).
- **Spy** - like a stub, but also records how it was used (call count, arguments), so the test can verify interactions afterward.
- **Fake** - a real but simplified working implementation (e.g., an in-memory database). Has actual behavior, just lighter than production.
- **Mock (full mock)** - simulates both the behavior and the interface of the dependency, with the test pre-programming expected calls and responses; used when you need complete control, including error simulation.

**Service virtualization** goes beyond simple mocking: it creates realistic simulations of complex services (message brokers, payment gateways) including latency, timeouts, and error states, for cases where the real service is unavailable or costly (Saini, integration-testing chapter; tools: WireMock, MockServer, Mountebank, Postman mock servers).

## Frameworks & models

**The AAA structure (Arrange-Act-Assert).** The most widely used micro-pattern for a single test: *Arrange* sets up the system and inputs, *Act* invokes the behavior under test, *Assert* checks the outcome. It gives every test a clear, consistent shape, which aids readability and diagnosis (Saini, Ch. 3). Compare Adzic et al.'s "Write assertions first" and "Given-When-Then in strict sequence," which serve the same clarity goal for specification-style checks.

**Data-driven and keyword-driven testing.** Saini's Chapter 1 catalogs these alongside AAA as foundational patterns: *data-driven* separates test logic from a table of input/expected pairs so one test body runs many cases; *keyword-driven* abstracts actions into reusable keywords so non-programmers can compose tests. Both reduce duplication and centralize maintenance.

**Layered test-distribution patterns (Saini, Ch. 2).** Six strategies for sequencing and allocating tests across layers:
- **Foundation-First Testing** - build coverage from the lowest layer up; vet each component in isolation before integrating, catching fundamental issues early and cheaply.
- **Layer-Specific Logic Testing** - automate each test at the same layer as the logic it validates, keeping tests relevant and effective.
- **Balanced Layer Distribution** - distribute tests across unit, service, and UI layers so no layer is over- or under-tested; optimizes resource use and resilience.
- **Simplicity-Driven Testing** - automate simpler tests first (often at less complex layers) for quick wins and early defect detection; expand iteratively.
- **Risk-Based Layer Coverage** - allocate testing effort by the risk/impact of defects in each layer, concentrating on areas critical to stability.
- **Feedback-Focused Testing** - prioritize tests in layers that give developers the fastest feedback, especially valuable in agile flow.

**Framework architecture (Saini, Chs. 7-9).** A maintainable automation framework: encapsulates UI interaction behind page objects, encapsulates API interaction behind client/service abstractions, centralizes test data generation, manages environment setup/teardown (Docker, in-memory or reset databases, fixtures), and integrates with CI/CD for parallel execution (pytest-xdist) and reporting. Design for scalability up front - parallel execution, large datasets, distributed runs.

## Patterns
*(Patterns also recorded in PATTERN-CATALOG.md under Domain 13.)*

- **Arrange-Act-Assert (AAA)** - Intent: give each test a single, clear three-phase shape. When to use: virtually every automated test. Trade-offs: discourages multi-behavior tests (a feature, not a bug); minimal overhead. *(Saini)*
- **Data-driven testing** - Intent: run one test body over a table of input/expected pairs. When to use: a rule with many equivalence cases. Trade-offs: centralizes maintenance and kills duplication; the table can grow unwieldy - keep it focused (compare Adzic et al. "Prefer smaller tables"). *(Saini)*
- **Keyword-driven testing** - Intent: compose tests from reusable named actions. When to use: tests authored or read by non-programmers; large repetitive flows. Trade-offs: an abstraction layer to build and maintain; enables broader contribution. *(Saini)*
- **Page Object Model (POM)** - Intent: encapsulate a screen's locators and interactions in one class so UI tests reference intent, not selectors. When to use: any non-trivial UI automation. Trade-offs: extra classes to maintain; isolates tests from UI churn so a markup change touches one place. *(Saini)*
- **Service / API client object** - Intent: wrap an API behind a reusable client so API-layer tests express calls semantically and share auth/session handling. When to use: API and contract testing. Trade-offs: indirection; centralizes endpoint/auth changes. *(Saini)*
- **Test data builder / factory** - Intent: construct valid test objects with sensible defaults and only the fields a test cares about overridden. When to use: tests needing structured domain objects. Trade-offs: a builder to maintain; removes brittle, repetitive setup (Saini cites FactoryBoy/Faker; compare Adzic et al. "Split data generators from tests"). *(Saini; Adzic et al.)*
- **Fixture (setup/teardown)** - Intent: share controlled setup and cleanup across tests (e.g., a pytest fixture creating a schema then dropping it). When to use: tests needing common, isolated state. Trade-offs: scope must be chosen carefully (function vs. session) to balance speed and isolation. *(Saini)*
- **Stub** - Intent: feed the unit fixed responses. When to use: the dependency's output matters but its interaction does not. Trade-offs: no interaction verification. *(Saini)*
- **Spy** - Intent: record interactions for later assertion. When to use: you must verify a call happened with given arguments. Trade-offs: tests can over-couple to implementation if overused. *(Saini)*
- **Fake** - Intent: a lightweight working implementation. When to use: you want realistic behavior without the real dependency's cost (in-memory DB). Trade-offs: the fake can drift from real behavior. *(Saini)*
- **Mock / full mock** - Intent: full control over behavior and interface, including error and edge simulation. When to use: simulating failures, timeouts, declined payments. Trade-offs: most coupling to implementation; over-mocking yields tests that pass while the system is broken (compare Adzic et al. on testability). *(Saini)*
- **Service virtualization** - Intent: simulate whole complex services with realistic latency/error behavior. When to use: real service unavailable, costly, or needs fault injection. Trade-offs: tooling and config overhead; far closer to production conditions than a bare mock. *(Saini)*
- **Foundation-First / Balanced / Risk-Based / Simplicity-Driven / Feedback-Focused / Layer-Specific layer-distribution patterns** - Intent: sequence and allocate tests across application layers. When to use: planning and growing a layered automation suite. Trade-offs: each biases coverage differently - combine them rather than pick one dogmatically. *(Saini)*
- **Provide atomic external resources** - Intent: give each test its own isolated external resource (account, queue, file) so tests do not collide. When to use: parallel or shared-environment runs. Trade-offs: provisioning overhead; removes a major flakiness source. *(Adzic et al.)*
- **Wrap synchronous DB tests in transactions** - Intent: run each DB test in a transaction rolled back at the end for automatic, fast cleanup. When to use: synchronous database integration tests. Trade-offs: not applicable to async or cross-transaction behavior. *(Adzic et al.)*

## Principles & best practices

- **Encapsulate volatility.** Put locators behind page objects and endpoints behind client objects so UI/API churn touches one place, not every test (Saini).
- **Isolate the unit under test.** Use the lightest double that still isolates: prefer a stub or fake over a full mock unless you must verify interactions or inject failures (Saini).
- **Generate test data deterministically and separately.** Split data generation from the test body so the same generators serve many tests and data issues stop masquerading as defects (Saini; Adzic et al. "Split data generators from tests").
- **Choose fixture scope deliberately.** Balance setup cost (session scope is fast) against isolation (function scope is clean).
- **Design for scale from the start.** Parallel execution, distributed runs, and large datasets are framework requirements, not afterthoughts (Saini).
- **Keep tests readable.** AAA shape, intent-revealing names, and one topic per test make a suite navigable years later (Saini; Adzic et al. "Optimise for reading, not writing").
- **Create a gallery of automation patterns.** Maintain worked examples of your house automation patterns so new tests are written consistently (Adzic et al. "Create a gallery of examples for automation patterns").

## Anti-patterns & pitfalls

- **Over-reliance on UI automation.** The slowest, most brittle layer; pushes maintenance up and feedback down. Move logic checks below the UI (Saini).
- **Over-mocking.** Tests that exercise mocks rather than real logic pass while the feature is broken (the "illusion of test reliability"). Prefer fakes and real collaborators where feasible.
- **Brittle, locator-coupled UI tests.** Skipping the page object so every test hard-codes selectors guarantees mass breakage on markup change (Saini).
- **Ad-hoc test data.** Inconsistent, shared, or hand-built data causes intermittent failures unrelated to real defects (Saini).
- **Frameworks that ignore scalability.** No parallelism or distributed support caps growth and slows feedback (Saini).
- **Patterns as dogma.** Applying one layer-distribution strategy everywhere; they are complementary lenses, not a single rule (Saini).

## Decision guidance

- **Picking a test double:** need fixed data only -> stub. Need to verify an interaction -> spy or mock. Want realistic behavior cheaply -> fake. Need to simulate failures/latency or a whole service -> mock or service virtualization. Default to the lightest double that isolates.
- **Structuring UI tests:** always behind a page object; assert on outcomes (URL, visible state) not internal steps.
- **Structuring API/contract tests:** behind a client/service object; centralize auth and session handling.
- **Distributing a new suite:** start Foundation-First to build a solid base, weight toward Risk-Based for critical areas, and use Feedback-Focused placement for the checks developers run most.
- **Managing test data:** builders/factories with defaults + per-test overrides; isolate per test via transactions, atomic resources, or fixtures.
- **When the framework fights you:** if maintenance dominates, the cause is usually missing encapsulation (no page/client objects), over-mocking, or unmanaged data - address those before adding tests.

## Source attribution

- **Test design patterns, AAA, data-/keyword-driven testing, layer-distribution patterns, page object model, fixtures, test doubles taxonomy, service virtualization, framework architecture and CI integration:** Saini (2025), Chapters 1-9.
- **Data-generator separation, atomic external resources, transaction-wrapped DB tests, automation-pattern gallery, readability and one-topic principles:** Adzic, Evans & Roden (2015), "Improving testability" and "Managing large test suites."

## Recent developments (2024-2026)

- **Playwright Agents (Planner, Generator, Healer).** Released with Playwright 1.56 in October 2025, these three coordinated AI agents autonomously plan, generate, and repair test suites, a notable shift from hand-authored automation. The Healer's self-healing reduces the locator-maintenance burden that this file flags as the central cost of UI automation, while Page Object Model and client-object encapsulation remain the recommended structure beneath the agents. (Microsoft Tech Community, 2025: https://techcommunity.microsoft.com/blog/azuredevcommunityblog/from-playwright-automation-to-agent-driven-testing-ghcp-in-action/4507395 ; Medium/Twinkle Joshi: https://medium.com/@twinklejjoshi/playwright-agents-the-future-of-intelligent-test-automation-3d2445fcb1c9)
- **Model Context Protocol (MCP) for browser automation.** Playwright MCP gives LLMs/agents a structured, semantic view of the application (accessibility snapshots, roles, labels, states) plus structured actions (click, type, navigate), letting AI drive real browser actions on stable semantic anchors instead of fragile selectors, an AI-era complement to the page-object pattern's goal of encapsulating volatility. (Medium/Kailash Pathak, 2025: https://kailash-pathak.medium.com/modern-test-automation-with-ai-llm-and-playwright-mcp-model-context-protocol-0c311292c7fb ; Testleaf: https://www.testleaf.com/blog/playwright-mcp-ai-test-automation-2026/)
- **Self-healing as a standard double/locator strategy.** AI now tries alternative locators or interaction paths when a selector breaks, directly reducing the "brittle, locator-coupled UI tests" and "ad-hoc test data" flakiness this file warns about; reference implementations pair POM with MCP servers and Pact-based contract tests. (BrowserStack, 2025: https://www.browserstack.com/guide/modern-test-automation-with-ai-and-playwright ; GitHub Playwright-AI-Agent-POM-MCP-Server: https://github.com/padmarajnidagundi/Playwright-AI-Agent-POM-MCP-Server)
- **Research on robust AI-generated unit tests.** 2025-2026 work moved from demonstrating LLM capability to engineering frameworks that ensure generated tests are correct and meaningful, including adversarial LLM-agent approaches (test vs. mutant) to strengthen unit-test quality, relevant to keeping AI-authored AAA tests valuable rather than coverage-theater. (arXiv "Test vs Mutant", 2026: https://arxiv.org/pdf/2602.08146)
- **Patterns endure; authoring changes.** AAA, data-/keyword-driven testing, the page/client object models, the test-doubles taxonomy, service virtualization, and the layer-distribution strategies remain the right structures; the 2024-2026 development is that AI agents increasingly generate and heal tests within these patterns, with over-mocking and missing encapsulation still the failure modes to guard against.
