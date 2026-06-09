# Software Engineering for Data (and Event-Database Architecture)

**Domain:** 08 - Data Architecture and Engineering
**Primary sources:** Software Engineering for Data Scientists: From Notebooks to Scalable Systems (Catherine Nelson, O'Reilly, 2024); Event-Database Architecture for Computer Games, Volume 1: Software Architecture and the Software Production Process (Rodney Quaye, CRC Press, 2026)

## Overview
This file pairs two complementary perspectives on getting data-centric code from a fragile prototype to a dependable system. Nelson addresses the practitioner gap: data scientists are excellent at analysis but often were never taught software engineering, so their notebook code is hard to reuse, maintain, test, or deploy. Her thesis is that as a data science codebase grows in size, lifespan, and number of contributors (including people who later leave), the value of software-engineering discipline compounds. She borrows decades of software practice (clean code, SOLID, testing, version control, packaging, CI/CD) and translates it for the data scientist moving "from notebooks to scalable systems."

Quaye approaches state and events from the opposite end: a database-centric architecture for an inherently stateful, event-driven domain (computer games). The Event-Database Architecture treats the entire game state as a Game Database and drives behavior through Primary and Secondary Events whose definitions live as Records in that database, with a complete Events History that makes the game replayable and resettable from any point. The two books meet on a shared theme central to data architecture and engineering: making stateful systems reproducible, testable, and recoverable, whether the "state" is an ML pipeline's outputs or a game world's events.

## Core concepts

### Software engineering for data scientists (Nelson)
- **Good code, five dimensions** - simplicity, modularity, readability, performance, and robustness. Good code is easy to maintain and adapt as requirements change, does not break on unexpected input, and lets you add a feature easily.
- **Code as craft** - there is inherent value in elegant, efficient code; the many small decisions made at the keyboard compound into quality or into debt.
- **Bit-rot** - code decays as its dependencies (libraries, OS) change; well-structured, documented code is far cheaper to revive.
- **Technical debt** - deferred work from writing code quickly instead of correctly (missing docs, poor structure, bad names); sometimes a necessary trade for deadlines but accrues interest in future bug-fixing.
- **Adapting to change** - writing code is not like building a bridge; requirements always shift, so adaptability matters more as the codebase grows and gets handed between people.
- **Reproducibility** - code that produces the same result given the same inputs; achieved through pinned dependencies, controlled randomness, error handling, and logging.
- **Notebook to script to package** - the maturation path: exploratory notebook, then extracted reproducible script (tools like nbconvert and Jupytext), then an installable package with managed dependencies.

### Event-Database Architecture (Quaye)
- **Game Database as central state** - a relational store holding the properties of all Events, all shared game data, the properties of all Game Objects, and abstract data used by the eight Host modules. State is data in Records, not scattered object fields.
- **Primary and Secondary Events** - Primary Events arrive at the Events Host from Game Objects or other Hosts; each Primary Event triggers one or more Secondary Events (Actions) sent to Game Objects. Event definitions and their relationships live as Records (Primary Event Record, Secondary Event Record), so designers, not only programmers, can author and rewire the chain of events and reuse an Action across different events.
- **Events History Record** - every Primary Event received and Secondary Event generated is appended, in chronological order, to a history before being acted on. This is the event log that enables replay, late-join synchronization, and bug reproduction.
- **Eight Host modules** - Events Host, Database Host, Objects Host, Physics Host, Graphics Host, Sounds Host, Game Controllers Host, and a Central Host that sets up and synchronizes the other seven.
- **Primary Initial Reset Event** - the standard event (sent by the Central Host at startup, and re-sendable by any Game Object) that resets the game world to its beginning from any point in the flow; the architecture's correctness test.

## Frameworks & models

### Nelson's progression (notebooks to scalable systems)
The book is a staged path mirroring its chapters: write good code (Ch 1), analyze and improve performance (Ch 2), choose data structures (Ch 3), apply OOP/functional styles (Ch 4), handle errors/logging/debugging (Ch 5), standardize with formatting/linting/type checking (Ch 6), test (Ch 7), design and refactor from notebook to script (Ch 8), document (Ch 9), share via version control/dependencies/packaging (Ch 10), expose via APIs (Ch 11), automate and deploy with CI/CD (Ch 12), secure (Ch 13), and work effectively in software teams with Agile practices (Ch 14).

### Testing model (Arrange-Act-Assert)
A test has four stages (per pytest): **Arrange** (set up inputs/data), **Act** (run the function), **Assert** (check the result matches expectation), and teardown. Two primary test types: **unit tests** (single function/unit) and **integration tests** (units working together). Test your own code, not imported libraries.

### When to test (data science nuance)
Testing is usually not worth it in the exploratory phase (you do not yet know what code survives). Add tests when code will be reused or modified, before deployment or before someone else uses it, when an unexpected error appears (a test pins the source), and before refactoring. Early tests catch simple mistakes (inconsistent names, missing imports, typos) that editors miss.

### Testing ML pipelines
Map tests to the ML project steps: data exploration (no tests unless code is reused), feature engineering (unit tests plus data validation), model training (specialized strategies), model evaluation (itself a kind of test; can be formalized as "accuracy must exceed X"), and model inference (specialized strategies). Randomness and non-determinism complicate ML testing but do not preclude it.

### Reproducibility and packaging model
Isolate dependencies in virtual environments (venv, Conda, or Poetry), record exact library versions, and turn scripts into installable packages so others can run the code reliably. This is the backbone of reproducibility.

### CI/CD and automation model
Automate repetitive workflow steps (linting, formatting, testing) so code is always standardized and validated before reaching production or even before commit. Pre-commit hooks run checks locally; GitHub Actions run tests in CI. Use multiple environments (staging/test then production), promote through them, and deploy reproducibly using Docker containers. CI/CD means Continuous Integration plus Continuous Deployment/Delivery.

### Event-Database Architecture model (Quaye)
- **Database-centric, event-driven design** - game logic is expressed as Records (events, priorities, object properties) in a Game Database, dispatched by the Events Host through the Objects Host to Game Objects, and synchronized by the Central Host.
- **Event priority and overlap resolution** - when several Secondary Events overlap (respond to the same Primary Event and share a Priority Events Record), the Events Host selects one probabilistically: priorities are summed, divided into intervals sized by each event's priority, and a random number selects the interval, so selection probability is proportional to relative priority. This lets two teams add conflicting features and keep both as mutually exclusive without deleting either.
- **Delayed events** - events can be scheduled: the Events Host adds a delay to current game time and inserts the event into an ordered Delayed Events List Record, dispatching when due.
- **Standard Primary Events** - every design includes a required set in chronological order: Initial Reset, Connect, Controller Moved, Collision, Proximity, End, and Shutdown, supplied by the relevant Hosts.
- **Replay/reset as correctness criterion** - the architecture is conformant only if the Primary Initial Reset Event can reset and replay the game from any point without error; the Events History must not be reset on shutdown/restart, only when the game world resets.

## Patterns

- **Notebook-to-script-to-package promotion** - Intent: graduate exploratory notebook code into reproducible scripts and then installable packages as it proves reusable. When to use: code that will run again, be shared, or enter production. Trade-offs: up-front effort to extract, structure, and package, repaid by reproducibility, testability, and reuse; skip it for genuine one-offs. *(Nelson, Ch 8 and 10.)*
- **Arrange-Act-Assert unit testing** - Intent: pin behavior with small deterministic tests structured as setup, execution, and assertion. When to use: reused/modified code, pre-deployment, after a surprising error, before refactoring. Trade-offs: confidence and safe change at the cost of writing/maintaining tests; not worth it in pure exploration. *(Nelson, Ch 7.)*
- **Automated quality gate (pre-commit plus CI)** - Intent: run linting, formatting, type checks, and tests automatically before commit and in CI so standards hold across a team. When to use: any shared or production codebase. Trade-offs: initial setup complexity, then near-zero ongoing effort and far fewer team mistakes. *(Nelson, Ch 6 and 12.)*
- **Reproducible deployment via containers and environments** - Intent: deploy through staging then production using pinned virtual environments and Docker images so runs are reproducible. When to use: production deployment of data/ML code. Trade-offs: container/tooling learning curve for guaranteed environment parity. *(Nelson, Ch 10 and 12.)*
- **Event-Database Architecture (database-centric event sourcing for stateful systems)** - Intent: hold all state in a central database and drive behavior through Primary/Secondary Events whose definitions are Records, recording every event in an append-only history. When to use: stateful, event-driven systems (games, simulations) needing designer-authorable behavior, multiplayer late-join synchronization, deterministic replay, and reproducible bug diagnosis. Trade-offs: full replay/reset, audit history, and decoupling of design from code, at the cost of routing all behavior through the database/event layer and maintaining the Records and history. *(Quaye, Ch 3.)*
- **Events History / replay-and-reset correctness test** - Intent: append every Primary and Secondary Event chronologically and require that the world can be reset and replayed from any point without error as the conformance test. When to use: systems where determinism, recovery, and reproducibility are first-class. Trade-offs: enables sync, replay, and debugging, but the history must never be reset except on world reset, and history length/temporal-or-spatial content must be designed up front. *(Quaye, Ch 3.)*
- **Priority-weighted probabilistic event resolution** - Intent: resolve overlapping mutually exclusive responses to the same event by selecting one with probability proportional to its priority. When to use: multiple features attach competing reactions to a single trigger and all should be retained but only one fires at a time. Trade-offs: keeps conflicting features without deletion and tunes likelihood via priority, at the cost of nondeterminism in selection (mitigated for replay by recording the chosen event in the history). *(Quaye, Ch 3.)*

## Principles & best practices
- Invest in code quality in proportion to a codebase's size, lifespan, and number of contributors; genuine one-offs can stay rough.
- Write simple, modular, readable, performant, and robust code; favor small functions and clear names over cleverness.
- Make code reproducible: pin dependencies in isolated environments, control randomness, handle errors, and log generously (err toward logging more, with appropriate severity levels).
- Test what you own, not your dependencies; add tests when code is reused, before deployment, after a surprising bug, and before refactoring.
- Automate linting, formatting, type checking, and testing so quality is standardized and enforced before code reaches production or even version control.
- Promote through staging before production and deploy reproducibly (containers); know the deployment basics even if another team owns the pipeline.
- For stateful, event-driven systems, centralize state and record an append-only event history so the system is replayable, recoverable, and debuggable; never discard the history except on a full reset.
- Express event behavior as data (Records) where possible so non-programmers can author and rewire it and so behavior is inspectable and reusable.

## Anti-patterns & pitfalls
- Leaving production-bound logic in untested, undocumented notebooks; it rots, resists handover, and breaks under new inputs.
- Accumulating silent technical debt (poor names, no docs, cut corners) under deadline pressure without ever returning to repay it.
- Skipping virtual environments and version pinning, so "works on my machine" code is not reproducible.
- Testing imported libraries instead of your own code, or trying to test exploratory code that will be thrown away.
- Treating ML non-determinism as a reason to write no tests at all, rather than formalizing thresholds and validating features/inference.
- In stateful/event systems: resetting or losing the event history on shutdown or crash, which destroys replay, multiplayer sync, and bug reproduction; the Quaye architecture explicitly forbids resetting the Events History except on world reset.
- Hardcoding event behavior in code so designers cannot author it and conflicting features must be deleted rather than coexisting via priorities.

## Decision guidance
- Decide how much engineering rigor to apply by asking whether the code will run again, be shared, or be handed over; the more yeses, the more it pays to promote notebook to script to package and add tests, docs, and CI.
- Add a test when you are about to reuse, deploy, hand over, or refactor code, or right after a surprising bug; do not bother during throwaway exploration.
- Automate the quality gate (pre-commit plus CI) once more than one person touches the code or it heads for production.
- Choose container-based, multi-environment deployment when reproducibility across environments matters.
- Choose a database-centric, event-sourced architecture (Quaye-style) when state is large and shared, behavior is event-driven, you need deterministic replay/reset and recovery, and you want non-programmers to author behavior; accept that all behavior must route through the database/event layer.
- Use the replay-and-reset test as the acceptance criterion for any system that claims to be event-sourced: if it cannot reset and replay from any point without error, the discipline has been broken somewhere (a hidden side effect or a discarded history).

## Source attribution
- Good code (five dimensions), code-as-craft, bit-rot, technical debt, adapting to change: Nelson, Chapter 1.
- Errors, logging (levels, configuration), and debugging: Nelson, Chapter 5.
- Formatting, linting, type checking: Nelson, Chapter 6.
- Testing (Arrange-Act-Assert, unit vs integration, when to test, ML testing): Nelson, Chapter 7.
- Design and refactoring, notebook-to-script: Nelson, Chapter 8.
- Documentation: Nelson, Chapter 9.
- Version control, dependencies, virtual environments (venv/Conda/Poetry), packaging: Nelson, Chapter 10.
- APIs (FastAPI): Nelson, Chapter 11.
- Automation and deployment, CI/CD, pre-commit hooks, GitHub Actions, Docker, environments: Nelson, Chapter 12.
- Security and working-in-software/Agile context: Nelson, Chapters 13 and 14.
- Event-Database Architecture, Game Database, Primary/Secondary Events, Events History, eight Host modules, Central Host, Primary Initial Reset Event, priority/overlap resolution, delayed events, standard Primary Events, replay/reset correctness: Quaye, Chapters 2 and 3.

## Recent developments (2024–2026)

- **Data and model versioning became a non-negotiable MLOps discipline.** 2025-2026 practice treats reproducibility as essential: tools like DVC and Pachyderm version datasets and pipeline runs, feature stores hold versioned feature definitions, and model registries track artifacts, extending Nelson's "pin dependencies, control randomness, reproduce results" beyond code to data and models. (Secoda, "Best Data Versioning Tools 2025," 2025, https://www.secoda.co/blog/best-data-versioning-tools-2025; Medium, "Data Versioning in MLOps," 2026, https://medium.com/@krushnabiradar44/data-versioning-in-mlops-ensuring-reproducibility-and-reliability-9ebb11edfa3e)
- **Open table formats supplied versioning/time travel as infrastructure.** Apache Iceberg's snapshot isolation and time travel let teams query data "as of" any point and roll back, reinforcing the reproducibility and replay-from-any-point goals this file shares between ML pipelines and event-sourced systems. (Medium, "Data Versioning in MLOps," 2026, link above)
- **AI assistants entered the software-engineering-for-data workflow.** Copilot-style tools now do code completion, suggest transformations, and generate pipelines from natural language, raising rather than lowering the value of Nelson's discipline (tests, linting, type checks, CI/CD) to keep AI-generated code maintainable and correct. (Microsoft Learn, "Overview of Copilot in Fabric," 2025, https://learn.microsoft.com/en-us/fabric/fundamentals/copilot-fabric-overview)
- **The notebook-to-production gap is increasingly closed by automated quality gates.** As more data-science code reaches production to serve AI/ML systems, the pre-commit-plus-CI quality gate and container-based reproducible deployment Nelson advocates became standard MLOps practice, with data quality treated as inseparable from model reliability. (Clarifai, "End-to-End MLOps Architecture & Workflow," 2025, https://www.clarifai.com/blog/end-to-end-mlops; lakeFS, "The State of Data and AI Engineering 2025," 2025, https://lakefs.io/blog/the-state-of-data-ai-engineering-2025/)
- **Event-sourcing / append-only-history thinking aligned with lakehouse and streaming defaults.** The shift to immutable logs (Kafka) and streaming-into-open-tables makes Quaye's "never discard the history; replay/reset from any point" correctness criterion a mainstream architectural property rather than a game-specific one. (Kai Waehner, "Data Streaming Meets Lakehouse," 2025, https://www.kai-waehner.de/blog/2025/11/19/data-streaming-meets-lakehouse-apache-iceberg-for-unified-real-time-and-batch-analytics/)
