# Quality Attributes / Architecture Characteristics Catalog

Consolidated catalog of architecture characteristics (a.k.a. quality attributes, non-functional requirements, "the -ilities") referenced across the knowledge base. Grouped by the Richards & Ford taxonomy (operational, structural, cross-cutting). Each entry gives a short definition. See `04-Software-Architecture-Fundamentals/architecture-characteristics-and-quality-attributes.md` for how to identify, prioritize, measure, and govern these.

Definition test (Richards & Ford): a true architecture characteristic (1) specifies a non-domain design consideration, (2) influences a structural aspect of the design, and (3) is critical or important to success.

## Operational characteristics
*How the system runs at runtime. Overlaps with operations/DevOps.*

- **Availability** - how long the system must be accessible/operational; fast recovery if it fails.
- **Continuity** - disaster-recovery capability.
- **Performance** - response time, throughput, stress/peak behavior, capacity; nuanced (averages vs maxima, performance budgets, first contentful paint, first CPU idle).
- **Recoverability** - how fast the system must come back online after a disaster; drives backup and redundancy.
- **Reliability / safety** - dependable, correct behavior over time; fail-safe where lives or large sums are at risk. Distinct from availability (a system can be available but unreliable).
- **Robustness** - handling error and boundary conditions (outages, hardware failures) while running.
- **Scalability** - performing well as the number of concurrent users/requests grows.
- **Elasticity** - handling sudden bursts of load (distinct from scalability).
- **Fault tolerance** - continuing to operate when parts fail.

## Structural characteristics
*Code-structure and quality concerns the architect owns or co-owns.*

- **Configurability** - end users can change configuration through usable interfaces.
- **Extensibility** - ease of plugging in new functionality.
- **Installability** - ease of installation on all target platforms.
- **Leverageability / reuse** - ability to reuse common components across products.
- **Localization / internationalization (i18n)** - multi-language, multi-currency, multi-unit support.
- **Maintainability** - ease of applying changes and enhancements.
- **Portability** - ability to run on more than one platform.
- **Upgradeability** - ease of moving from one version to the next.
- **Modularity** - composed of discrete, well-separated components (an implicit but pervasive characteristic).
- **Deployability** - ease, frequency, and low risk of deployment.
- **Testability** - completeness and ease of testing (measurable via coverage).
- **Agility** - composite = modularity + testability + deployability (often mistaken for time-to-market).

## Cross-cutting characteristics
*Important constraints that defy easy categorization.*

- **Accessibility** - usable by all users, including those with disabilities.
- **Archivability** - data archived or deleted after a defined period.
- **Authentication** - ensuring users are who they claim to be.
- **Authorization** - ensuring users can access only permitted functions/data.
- **Legal / regulatory** - compliance constraints (GDPR, SOX, data protection).
- **Privacy** - hiding transactions/data even from internal staff (e.g., encryption from DBAs).
- **Security** - protecting data and access (encryption in transit and at rest, confidentiality, integrity, non-repudiation, accountability, authenticity). An implicit characteristic in nearly every system.
- **Supportability** - level of technical support, logging, and debugging facilities required.
- **Usability / learnability** - effort required for users to achieve their goals.
- **Interoperability** - ease of integration with other systems (implies published, documented APIs).
- **Compatibility** - adherence to industry/domain standards; coexistence with other systems.
- **Data integrity** - preserving correct data and relationships (e.g., a reason to choose a relational database).

## Notes on use
- **Trade-offs (First Law):** improving one characteristic usually degrades another (security vs performance). Aim for the least-worst set.
- **Prioritize to the top three** rather than ranking all; over-specifying is as harmful as under-specifying.
- **Scope at the architecture quantum,** not the whole system; differing characteristics across parts can force a distributed/hybrid architecture.
- **Measure and govern** each chosen characteristic objectively, ideally with a fitness function wired into CI.

*Primary source: Fundamentals of Software Architecture, Chapters 4–7 (Richards & Ford, 2020); reinforced by Head First Software Architecture (Gandhi, Richards & Ford, 2023) and Software Architecture: The Hard Parts (Ford et al., 2021).*
