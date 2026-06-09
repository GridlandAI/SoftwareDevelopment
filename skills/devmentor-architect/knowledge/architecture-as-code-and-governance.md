# Architecture as Code and Governance

**Domain:** 04 - Software Architecture Fundamentals
**Primary sources:** Architecture as Code (Ford, Richards, 2025); Building Evolutionary Architectures, 2nd Edition (Ford, Parsons, Kua, Sadalage, 2022)

## Overview
Architectural decisions have always been easy to *document* and hard to *enforce*. Architecture Decision Records (ADRs) capture context and rationale, but no one consults them in the heat of a deadline, and a well-intentioned developer can introduce a coupling point that quietly degrades the architecture. Architecture as Code closes that gap: it expresses architectural intent in an executable, machine-checkable form so that the structure and constraints an architect designed are continuously verified against what the team actually builds.

The discipline rests on two pillars. The first is the *architectural fitness function* - an objective integrity check on architecture characteristics, wired into continuous integration like a unit test. The second is a lightweight **Architecture Definition Language (ADL)**: a platform-neutral pseudo-code that declares domains, services, components, and constraints once, from which concrete fitness functions can be generated (increasingly with LLMs) for any technology stack. Crucially, Architecture as Code is framed as a *feedback* framework, not a testing framework: a failing architectural check is usually a placeholder for a conversation, not a defect. This matters across the SDLC because it turns architecture from a one-time diagram into a living, governed, quantified artifact that stays aligned with the code as both evolve.

## Core concepts

**Architecture as Code.** Expressing architectural structure and constraints as executable artifacts so that gaps between the *intended* architecture and the *implemented* one are detected automatically and quickly. It is deliberately *less* comprehensive than domain testing - the goal is to govern the critical, load-bearing parts (like a building architect governing load-bearing walls, not the carpet), not to wrap a net around every concern.

**Architecture Definition Language (ADL).** A lightweight, declarative pseudo-code for describing architecture concerns succinctly and platform-independently. Structure:
- Metadata lines: `DESCRIPTION` and `CATEGORY` (for example, `Structural`).
- `DEFINE` verb associates a logical name with a physical location (directory or namespace): `DEFINE SYSTEM Sysops Squad AS sysops`, then `DEFINE DOMAIN`, `DEFINE SERVICE`, etc.
- `ASSERT` specifies the test condition: `ASSERT(CLASSES are only CONTAINED within SUBDOMAINS within DOMAINS)`.
ADL is the single source of truth from which executable fitness functions are generated. It is readable by non-developers (unlike raw Java/C#) and avoids the maintenance headache of multiple per-platform versions. Architects are not required to use ADL - a single-platform Java shop can keep fitness functions in Java - but ADL pays off in heterogeneous environments and for communication.

**Feedback framework, not a testing framework.** When a unit test fails, it signals an error. When an ADL constraint fails, it is more often a *feedback opportunity* - a placeholder for a conversation. Example: a developer adds a new component that trips a structural fitness function; perhaps the component is legitimate and the architect updates the ADL and re-analyzes coupling, or perhaps it should have gone into an existing component. The failing check triggers collaboration rather than blame.

**Structural alignment.** For governance to work, four artifacts must align: the logical/physical architecture diagram, the ADL, the generated executable fitness functions, and the source code structure (directories, packages, namespaces). Pulling the architect into naming and structure traditionally owned by developers creates friction; mitigate it by letting developers name directories (matching the architecture) and writing ADL to match, and by framing the checks as fast feedback rather than control.

**Constraints alignment.** Architectural constraints (for example, "the UI cannot access the database directly," "all inter-service communication uses request/reply messaging," open vs. closed layers) come from architectural decisions and are documented in ADRs - but ADRs cannot enforce them. ADL can describe these constraints, and the generated fitness functions enforce them in CI. This is how decisions like "restrict database-related classes to the persistence layer" become continuously governed rather than aspirational.

**Architecture vs. design (the governance boundary).** What is worth governing as architecture sits on a spectrum versus design, judged by: strategic vs. tactical (more thought, more people, longer-lived = more architectural); level of effort to change ("the stuff that's hard to change"); and significance of trade-offs (bigger trade-offs = more architectural). Governance focuses on the architectural end of the spectrum.

## Frameworks & models

### Fitness-function-driven architecture (FFDA)
Define the architecture's domains and constraints in ADL first, generate failing fitness functions, then refactor the source structure to make them pass - analogous to test-driven development. For brownfield systems, use the *Flattening pattern* (from Software Architecture: The Hard Parts): push all source code down to leaf-node directories to create definable components, then group components into subdomains and domains. FFDA is explicitly *not* about comprehensive coverage; it targets the critical breaking points where fast feedback prevents drift.

### Generating fitness functions from a single source of truth
Two strategies translate ADL pseudo-code into concrete, platform-specific fitness functions:
1. **Code generators / DSL** - write a generator that emits, for example, both ArchUnit (Java) and NetArchTest (.NET) tests. Historically a full DSL was prohibitive to build.
2. **Generative AI (LLMs)** - give the ADL to an LLM and have it emit ArchUnit, PyTestArch, NetArchTest, or plain scripts. This is *translation/interpretation*, not deterministic transpiling, so the output must be vetted before use. LLMs handle small, contained ADL files well but falter on large, deeply nested ones - hence the recommendation to split ADL files (one for domains, one per domain) rather than describe the whole system in a single file.

When the ADL changes, the architect can regenerate the source code or directly modify the previously generated code; for anything short of a massive restructuring, directly modifying the existing executable code is usually recommended (since generated code often needs hand-tweaking anyway).

### From qualitative to quantitative trade-off analysis
Architecture as Code is the fourth book in a trade-off-analysis lineage (Fundamentals of Software Architecture; Software Architecture: The Hard Parts; Software Architecture Patterns, Antipatterns, and Pitfalls).
- **Qualitative analysis** - before a system is built, compare architecture styles by how well they support each architecture characteristic, using a five-star scale (chosen because 20% increments are fine-grained enough to distinguish styles without implying false precision). Ratings are derived from understanding which structural choices enable which characteristics (for example, asynchronous communication + eventual consistency + choreography maximize scalability), not from building all candidates.
- **Quantitative analysis** - measure working software directly. This is preferred when possible; the whole point of Architecture as Code is to react to ecosystem changes by applying architectural feedback as code, making characteristics measurable and governed continuously rather than spot-checked.

### Code-level metrics for governance
Quantifiable architecture health metrics that make good fitness functions:
- **Cyclomatic complexity (CC)** - `CC = E − N + 2P` (edges, nodes, connected components). Industry threshold ~10; the authors prefer under 5. Cannot distinguish essential from accidental complexity, so context matters. Govern with a per-method or average-CC threshold; use cascading thresholds to herd legacy code toward the target.
- **Coupling** - afferent (incoming) and efferent (outgoing) connections; dense coupling produces a Big Ball of Mud where any change ripples everywhere.
- **Abstractness (A)** - ratio of abstract to concrete artifacts.
- **Instability (I)** - `Ce / (Ce + Ca)`; how easily a component breaks when changed.
- **Normalized distance from the main sequence (D)** - `|A + I − 1|`; how balanced a component is. Far into the upper-right is the *zone of uselessness* (too abstract); far into the lower-left is the *zone of pain* (too concrete/brittle). Useful both for evaluation before restructuring and as an ongoing fitness function.

## Patterns

**ADL-described domains/services** - Intent: govern that the code's high-level structure matches the intended domains (monolith) or services (distributed). When to use: any system where structural drift (a new top-level directory silently creating a new domain) should trigger review. Trade-offs: requires source-structure alignment and some architect-developer negotiation over naming; in return, structure stays continuously aligned and new components surface a conversation. Granularity tip: one ADL file for domains, separate files per domain, to keep LLM generation reliable and limit regeneration churn.

**ADL-described constraints** - Intent: enforce architectural decisions (layer access rules, allowed communication paths, persistence isolation) that ADRs can only document. When to use: whenever a decision supports a critical architecture characteristic and could be silently violated. Trade-offs: turns prose decisions into executable guardrails; the architect must keep ADL, diagram, and tests in sync as decisions evolve.

**Pseudo-code → generated cross-platform fitness function** - Intent: declare a rule once and govern it across a polyglot estate. When to use: heterogeneous stacks where no single tool (like ArchUnit) covers everything. Trade-offs: single source of truth and readability for non-developers; LLM output is non-deterministic and must be reviewed; multiple concrete formats are kept only for convenience/readability, not correctness.

**Log-harvesting governance script** - Intent: govern distributed-system behavior (for example, services communicating only with an orchestrator) by parsing consistent, observable log output. When to use: microservices where compile-time checks are impossible because each service differs in language and repo. Trade-offs: reactive (a time lag, for example 24 hours) with no runtime overhead; for security-critical rules prefer a proactive real-time monitor instead, accepting some overhead. Most such fitness functions are 10–20 lines of "glue" in a scripting language that aggregate existing data.

**Governance against a known negative trade-off** - Intent: when an architectural decision (for example, feature branching vs. feature toggles) carries an accepted negative trade-off, build a fitness function that raises an alarm if that negative manifests. When to use: any consciously accepted trade-off ("everything in software architecture is a trade-off"). Trade-offs: extra fitness functions to maintain; protection against the downside materializing unnoticed.

## Principles & best practices
- **Govern critical breaking points, not everything.** Coverage goals for Architecture as Code are focused, not comprehensive - mirror the building architect's concern for load-bearing structure.
- **Objective measure + alarm = fitness function.** Wiring a metrics or monitoring tool into the build does nothing unless it produces an unambiguous output and raises an alarm on threshold breach. Many teams run SonarQube but never look at the results - that is not governance.
- **Keep ADL files small and split by domain** so LLM generation stays reliable and changes do not force regenerating unrelated code.
- **Vet generated code.** LLM translation of ADL is interpretive, not deterministic; treat generated fitness functions as drafts to review.
- **Treat failures as conversations.** A tripped architectural check is a feedback opportunity to update the ADL/diagram/tests or to discuss the design - not automatically a bug to "fix."
- **Reduce friction deliberately.** Explain *why* structural alignment matters, let developers own naming, and delegate ADL/diagram/test maintenance to a lead developer who confers with the architect - this also deepens the team's understanding of the architecture.
- **Prefer atomic fitness functions; use holistic ones for real interactions** (for example, security vs. responsiveness when on-the-fly encryption is added).
- **Write governance as cheap glue.** Most fitness functions aggregate existing outputs (logs, metrics) in a short script in whatever language is convenient - building an entire framework is rarely necessary.
- **Continuous database integration.** Govern schema alignment (for example, ORM mappings staying in sync with schemas) the same way as code, since source and schema are symbiotic.

## Anti-patterns & pitfalls
- **ADRs without enforcement.** Documenting a decision and assuming it will be honored; teams rarely consult ADRs under pressure. Pair every governable decision with a fitness function.
- **One giant ADL file.** LLMs falter on large, deeply nested ADL, and any small change forces regenerating the whole executable class. Split by domain.
- **Trusting generated code blindly.** Generative AI is not a deterministic compiler; unreviewed output can be wrong.
- **Treating Architecture as Code like domain testing.** Chasing comprehensive coverage of every architectural concern is time-consuming and impractical; it also misframes failures as defects rather than feedback.
- **Inappropriate governance (homogenization).** Forcing one technology stack on every project made sense when resources were expensive and shared, but in a cheap-container world it imposes the most-complex case on every team. Use "just enough" governance (a small set of standard stacks) and let requirements pick.
- **Mistaking measurement for governance.** Having a monitor or metric without an objective threshold and alarm gives the illusion of control without the substance.

## Decision guidance
- Ask first whether a concern is *architecture* (strategic, hard to change, significant trade-offs) and *critical* (load-bearing). Govern those; leave design-level concerns to ordinary review.
- Decompose each architecture characteristic until it is objectively measurable, then choose a metric/tool and wire it into CI with a threshold and alarm - only measurable things can be governed.
- Choose where to declare the rule: keep it in the native language for a single-platform shop; use ADL when you have multiple stacks, want non-developer readability, or need a single source of truth.
- Choose the enforcement style by criticality: proactive real-time monitors for security-critical rules; reactive log-harvesting or pipeline checks for structural rules where runtime overhead is unwelcome.
- For brownfield alignment, define the target ADL and failing fitness functions, then apply the Flattening pattern and refactor toward green.
- Use qualitative five-star analysis to choose a style before building; switch to quantitative, code-governed measurement once software exists.
- Frame and communicate every architectural check as fast feedback that triggers a conversation, to keep developer buy-in and avoid the "architect as cop" dynamic.

## Source attribution
- ADL syntax, structural alignment, constraints alignment, feedback-vs-testing framing, splitting ADL files, regenerate-vs-modify, FFDA, Flattening pattern, architecture-vs-design spectrum: Architecture as Code (Ford, Richards, 2025), Ch 1–4.
- Fitness function definition, atomic/holistic, objective-measure-plus-alarm, cross-platform generation via LLMs, log-harvesting orchestrator example, governance against negative trade-offs: Architecture as Code, Ch 1.
- Qualitative-to-quantitative trade-off analysis and five-star scale rationale: Architecture as Code, Ch 1 (drawing on Fundamentals of Software Architecture and Software Architecture: The Hard Parts).
- Code-level metrics (cyclomatic complexity, coupling, abstractness, instability, distance from the main sequence, zones of pain/uselessness): Architecture as Code, Ch 5, and Building Evolutionary Architectures 2e, Ch 4.
- Fitness functions as automated governance, XP/DevOps lineage, ArchUnit/linters, communication governance, enterprise platform governance, documenting via ADRs/BDD/Jupyter, "just enough" governance, inappropriate-governance antipattern: Building Evolutionary Architectures 2e, Ch 4 and Ch 8 (Ford, Parsons, Kua, Sadalage, 2022).

## Recent developments (2024–2026)

- **The book is now published (2025).** *Architecture as Code* (Ford & Richards, O'Reilly) moved from a draft concept to a released title in 2025, positioning architecture as the last major discipline to become "as code" after infrastructure, policy, and data. It presents the ADL + fitness-function + automated-feedback framework as a practical, organization-wide way to define structure, constraints, governance, security, and communication. (O'Reilly book page, 2025, https://www.oreilly.com/library/view/architecture-as-code/9798341640368/)

- **Generative AI as the default ADL-to-fitness-function path.** Ford now frames "taking a lightweight definition language and generating concrete fitness functions" as one of the most obvious uses of generative AI, and argues that the arrival of MCP and agentic AI has "largely solved" the brittleness that previously made enterprise-architecture governance impractical. This strengthens the book's LLM-generation strategy over hand-built DSLs. (O'Reilly Radar, "How Agentic AI Empowers Architecture Governance," 2025, https://www.oreilly.com/radar/how-agentic-ai-empowers-architecture-governance/)

- **ADL as a contract for AI coding agents.** Beyond governing humans, the ADL is increasingly pitched as the way to "specify architecture to agents," so LLM-driven code generation is checked against the same executable constraints. The failing-check-as-feedback framing becomes especially important when an agent, not a person, tripped the rule. (DDD Academy, "Architecture as Code," 2025, https://ddd.academy/architecture-as-code/)

- **ADR enforcement tooling for AI agents.** New tools (e.g., Mneme HQ) turn ADRs into deterministic pre-generation checks for Claude Code, Cursor, and Copilot, operationalizing this file's "ADRs without enforcement" antipattern by wiring recorded decisions directly into the AI coding loop. (adr.github.io tooling list and related 2025 tooling discussion, https://adr.github.io/adr-tooling/)

- **Automated architecture-documentation generation.** Academic work such as CIAO uses LLMs to derive architecture documentation and structural views from code, a complement to governance-as-code where the "current state" picture is reconstructed automatically rather than maintained by hand. (arXiv, "CIAO - Code In Architecture Out," 2026, https://arxiv.org/pdf/2604.08293)

- **Core theory is stable.** The ADL syntax, FFDA, Flattening pattern, structural/constraints alignment, and code-level metrics are unchanged from the 2025 book; the live edge is AI-assisted generation, agent governance, and enforcement integration.
