# Domain-Driven Design: Strategic Design

**Domain:** 06 - Software Design Patterns and DDD
**Primary sources:** Learning Domain-Driven Design (Vladik Khononov, 2021); Implementing Domain-Driven Design (Vaughn Vernon, 2013)

## Overview
Strategic domain-driven design (DDD) is the part of DDD concerned with the big-picture decomposition of a software system to match the structure and strategy of the business it serves. Where tactical design tells you how to implement a model inside one component, strategic design tells you what the components should be, where their boundaries belong, what language each one speaks, and how they integrate. The central claim is that the cost and risk of large systems come less from individual algorithms than from misaligned boundaries, ambiguous language, and unmanaged coupling between teams. Strategic DDD addresses these by aligning software boundaries with business subdomains, protecting a consistent language inside explicit boundaries (bounded contexts), and making the relationships between those boundaries deliberate rather than accidental.

Strategic design matters across the whole SDLC. It frames requirements analysis (what is core versus generic), drives architecture (service boundaries, build-versus-buy), shapes team organization (who owns what), and governs integration contracts. Getting boundaries roughly right early is far cheaper than refactoring physical boundaries later, so strategic DDD is most valuable at project inception and during major restructurings.

## Core concepts

### Business domain and subdomains
A **business domain** is the company's main area of activity, the service it provides to customers (FedEx: courier delivery; Starbucks: coffee). A company can operate in several business domains. A **subdomain** is a fine-grained area of activity; all of a company's subdomains together form its business domain. Identifying subdomains and their types is the entry point of strategic design because each type warrants a different implementation strategy. (Khononov 2021, ch. 1; Vernon 2013, ch. 2)

DDD distinguishes three subdomain types:

- **Core subdomain** - what the company does differently from competitors; the source of competitive advantage. Naturally complex (a simple solution gives only short-lived advantage), highly volatile (continuously evolved), and must be built in-house by the most skilled engineers using the most advanced techniques. The competitive advantage is not always technical (jewelry design, manual fraud analysis). Also called the *core domain*. (Khononov 2021, ch. 1)
- **Generic subdomain** - complex but already solved, used the same way by all companies (authentication, encryption, accounting). No competitive edge. Buy or adopt an off-the-shelf or open-source solution rather than build.
- **Supporting subdomain** - supports the business but provides no competitive advantage and has simple, CRUD/ETL-style logic with low entry barriers. Must usually be built in-house (no ready-made solution exists) but can use rapid-application-development frameworks, junior staff, or outsourcing.

A useful tie-breaker: ask whether a subdomain could be turned into a profitable side business (then it is core), and whether it would be cheaper to hack your own version than integrate a third party (then supporting rather than generic).

| Subdomain | Competitive advantage | Complexity | Volatility | Strategy |
| --- | --- | --- | --- | --- |
| Core | Yes | High | High | Build in-house, best engineers |
| Generic | No | High | Low | Buy / adopt |
| Supporting | No | Low | Low | Build in-house / outsource |

**Distilling subdomains:** start from coarse-grained organizational units (departments) and drill down into finer-grained, coherent sets of use cases (same actor, related data). Distill core subdomains aggressively to extract embedded generic and supporting functionality and focus effort on the truly differentiating part. Relax distillation for generic/supporting subdomains once further drilling reveals no new design-relevant insight. (Khononov 2021, ch. 1)

### Domain experts
Domain experts are the subject-matter authorities from whom all business knowledge originates: the people who identified the business problem and the software's end users, not the analysts or engineers. Effective DDD depends on direct, continuous conversation with them. (Khononov 2021, ch. 1; Vernon 2013, ch. 1)

### Ubiquitous language
The ubiquitous language is the cornerstone practice of DDD: a single, shared language describing the business domain, used by everyone (engineers, product, domain experts, designers) in conversation, requirements, tests, documentation, and source code. It replaces the lossy chain of translations (domain knowledge to analysis model to requirements to design to code). Rules: it is the language of the business (no technical jargon); each term has exactly one meaning (no ambiguity, no synonyms used interchangeably); it must be cultivated continuously and evolve as domain understanding deepens. (Khononov 2021, ch. 2; Vernon 2013, ch. 1)

A useful model is just enough abstraction to solve the problem at hand ("all models are wrong, but some are useful"). A model needs a purpose and therefore a boundary. Tools that help capture the language: a shared glossary/wiki (good for nouns), and behavior-capturing tools such as use cases and Gherkin/executable specifications (good for rules and invariants). Tools are secondary to actual daily use of the language.

### Bounded context
On an organizational scale, domain experts' mental models are often inconsistent: the same term (for example "lead", or "policy", or "user") means different things in different parts of the business. A single enterprise-wide model becomes a "jack of all trades, master of none." The **bounded context** resolves this by dividing the ubiquitous language into smaller, internally consistent languages, each scoped to an explicit context. Within a bounded context a term has one meaning; across contexts the same word may mean different things. A bounded context is the boundary of a model and its language. (Khononov 2021, ch. 3; Vernon 2013, ch. 2)

Key properties:
- **A model needs a boundary.** Without one it expands toward a copy of the real world. The problem the model solves is part of the model.
- **Bounded contexts vs subdomains.** Subdomains are discovered (defined by business strategy); bounded contexts are designed (a solution-space decision). A bounded context can contain one or several subdomains; the mapping is not forced to be one-to-one.
- **Ownership.** A single team should own a bounded context. One team can own multiple bounded contexts, but a bounded context should not be split across teams (shared kernel is the deliberate exception).
- **Physical vs logical.** Bounded contexts can be physical (separate services) or start as logical modules inside a monolith and be split later.

### Sizing heuristic
Size is one of the least useful heuristics for a bounded context; treat size as a function of the model, not the other way around. Start with wider boundaries (encompassing multiple subdomains), especially around volatile core subdomains where you are most likely to be wrong, because refactoring logical boundaries is far cheaper than refactoring physical ones. Decompose into smaller contexts later as domain knowledge solidifies. A change that repeatedly spans multiple bounded contexts signals badly placed boundaries. (Khononov 2021, ch. 10)

## Frameworks & models

### Context map
A context map is a visual model of all bounded contexts and the integration relationships between them. It reveals high-level design, the communication patterns between teams, and organizational issues (for example, if every consumer of one upstream context resorts to an anticorruption layer, or all "separate ways" cluster around one team, there is an organizational signal). Introduce it early, maintain it as a shared effort (each team maintains its own integrations), and consider managing it as code (Context Mapper). A single pair of contexts can exhibit multiple patterns at once when they span multiple subdomains. (Khononov 2021, ch. 4)

### Relationship to team topologies and Conway's Law
The context-mapping patterns are fundamentally descriptions of *team collaboration modes*, not just code dependencies. Cooperation patterns presume well-communicating, mutually dependent teams; customer-supplier patterns presume a power imbalance between an upstream and a downstream team; separate ways presumes teams that will not or cannot collaborate. This makes the context map a lens on the organization (Conway's Law): software boundaries and team boundaries co-evolve, and the integration pattern you can sustain depends on how the owning teams actually communicate. Choosing a bounded-context decomposition is implicitly choosing a team structure. (Khononov 2021, ch. 4; Vernon 2013, ch. 3)

## Patterns
Strategic patterns are grouped by the collaboration mode between the teams owning the bounded contexts. For each: intent; when to use; trade-offs.

### Cooperation (well-communicating, mutually dependent teams)

- **Partnership** - Intent: integrate two contexts via ad hoc, two-way coordination; either team can propose a contract change and the other adapts, no single team dictates. When to use: teams with strong, frequent communication and dependent goals, ideally co-located. Trade-offs: needs continuous integration and high commitment; poor fit for geographically distributed teams.
- **Shared kernel** - Intent: a small, shared, co-owned model (ideally just integration contracts and shared data structures) implemented once and used by multiple contexts, kept consistent across all of them. When to use: when the cost of duplication exceeds the cost of coordination (typically volatile core models), when partnership communication is impossible (geography, politics), during gradual legacy modernization, or to make integration explicit between contexts owned by the same team. Trade-offs: couples the lifecycles of participating contexts (a change affects all immediately); violates the single-owner principle, so use sparingly and keep the shared scope minimal with integration tests on every change.

### Customer-supplier (power imbalance; upstream supplier, downstream consumer)

- **Conformist** - Intent: the downstream context simply adopts the upstream context's model as-is. When to use: power favors upstream (external provider or politics) and the upstream model is good enough or an industry standard. Trade-offs: downstream gives up modeling autonomy; cheap but couples the consumer to a foreign model. Never appropriate for a downstream core subdomain.
- **Anticorruption layer (ACL)** - Intent: the downstream context translates the upstream model into its own model behind a protective layer. When to use: downstream is a core subdomain, the upstream model is messy/inconvenient (often legacy), or the upstream contract changes often. Trade-offs: extra translation code to build and maintain, in exchange for protecting the consumer's language and isolating it from upstream churn. A shared ACL serving several consumers becomes an *interchange context*.
- **Open-host service (OHS) / published language** - Intent: the upstream supplier protects its consumers by decoupling its internal model from a public, integration-oriented contract (the published language), which it can version and evolve independently. When to use: power favors the consumers and the supplier wants to serve them well; many downstream consumers. Trade-offs: the mirror image of an ACL (the supplier pays the translation cost); enables multiple simultaneous published-language versions for gradual migration. Generic and supporting upstream subdomains are common OHS candidates.

### Separate ways (no collaboration)

- **Separate ways** - Intent: do not integrate at all; duplicate the functionality in each context. When to use: communication is too costly (size, politics); the subdomain is generic and trivially integrated locally (for example a logging library); or model differences are so large that an ACL would cost more than duplication. Trade-offs: avoids coordination cost at the price of duplicated code. Never use for core subdomains.

### Failure mode
- **Big ball of mud** - Intent (anti-pattern, named for completeness): no discernible boundaries; the ubiquitous language is inconsistent and models bleed into each other. This is the default state strategic DDD exists to prevent. Appropriate vertical boundaries inside a context (modular monolith) keep a bounded context from degenerating into one.

## Principles & best practices
- Start strategic design by identifying business domains, subdomains, and subdomain types; let that drive build-versus-buy, staffing, and where to invest engineering rigor. (Khononov 2021, ch. 1)
- Cultivate one ubiquitous language per bounded context, in the language of the business, with one meaning per term, reinforced everywhere including the code. (Khononov 2021, ch. 2; Vernon 2013, ch. 1)
- Give every model an explicit purpose and boundary; a bounded context is that boundary. (Khononov 2021, ch. 3)
- One team owns a bounded context; choose context-mapping patterns to match how teams actually collaborate. (Khononov 2021, ch. 4)
- Prefer wide boundaries early around volatile core subdomains; split into finer contexts as knowledge grows. (Khononov 2021, ch. 10)
- Maintain a living context map as a shared, ideally code-managed artifact. (Khononov 2021, ch. 4)

## Anti-patterns & pitfalls
- **Enterprise-wide single model.** One model for all problems collapses under the weight of filtering, finding, and keeping data consistent. Split into bounded contexts.
- **Prefixing terms instead of contexts** (for example "marketing lead" vs "sales lead" everywhere): induces cognitive load and is not how people actually speak. Use bounded contexts instead.
- **Equating bounded contexts with microservices and optimizing for smallness.** Size is the weakest boundary heuristic; premature small contexts produce expensive cross-context changes.
- **Shared kernel sprawl.** A large or casually shared kernel couples teams and re-creates the very inconsistency bounded contexts were meant to remove.
- **Conformist or separate ways on a core subdomain.** Both undermine the company's strategy to build its differentiator with care.
- **Treating the context map as a one-time diagram.** It must be maintained or it silently drifts from reality.

## Decision guidance
- **Classify each subdomain first.** Core (build in-house, invest), generic (buy/adopt), supporting (build cheaply or outsource). Re-examine the classification if the chosen implementation pattern (see tactical file) does not match the assumed type.
- **Decide the bounded-context decomposition** by where the ubiquitous language stays consistent and a single team can own the result. Default to wider contexts around core/volatile subdomains.
- **Choose a context-mapping pattern** from the collaboration reality:
  - Teams collaborate well and depend on each other → partnership; extract a shared kernel only when duplication clearly costs more than coordination.
  - Power favors upstream, model acceptable → conformist; model unacceptable or downstream is core or upstream churns → anticorruption layer.
  - Power favors downstream consumers → open-host service with a published language.
  - Collaboration is infeasible, the subdomain is generic, or models diverge sharply → separate ways (never for core).
- **Use the context map** to detect organizational dysfunction, not just code dependencies, since the patterns encode team communication modes.

## Source attribution
- Subdomains, subdomain types, distillation, domain experts: Khononov 2021, ch. 1; reinforced by Vernon 2013, ch. 2.
- Ubiquitous language, modeling, tooling: Khononov 2021, ch. 2; Vernon 2013, ch. 1.
- Bounded contexts, inconsistent models, boundaries (physical/logical/ownership), bounded contexts vs subdomains: Khononov 2021, ch. 3; Vernon 2013, ch. 2.
- Context-mapping patterns (partnership, shared kernel, conformist, ACL, OHS/published language, separate ways) and context maps: Khononov 2021, ch. 4; Vernon 2013, ch. 3 and ch. 13.
- Bounded-context sizing heuristic and wide-then-split guidance: Khononov 2021, ch. 10.
- Team-collaboration framing of the patterns / Conway's Law connection: Khononov 2021, ch. 4; Vernon 2013, ch. 3.

## Recent developments (2024–2026)
- **LLMs accelerate (do not replace) strategic design.** A 2025 strand of practice uses large language models as analytical partners to draft subdomain maps, sharpen the ubiquitous language, and propose bounded-context heuristics, while keeping the human-driven rigor of boundaries and one-meaning-per-term. The consensus is that LLMs speed up discovery artifacts but the strategic judgment (what is core, where boundaries belong) stays human. (ddd.academy, "Accelerate your Strategic Design with LLMs", 2025, https://ddd.academy/accelerate-your-strategic-design-with-llms/; nidly.substack.com, "Domain Driven Design in the AI Era", 2025, https://nidly.substack.com/p/domain-driven-design-in-the-ai-era)
- **EventStorming and Domain Storytelling become "context feeders" for AI.** Collaborative-modeling outputs are now used to give LLMs the domain context they need to generate aggregates, commands, and events more accurately, tightening the loop from workshop to code. (codecentric, "From Stories to Code", 2025, https://www.codecentric.de/en/knowledge-hub/blog/from-stories-to-code-how-domain-storytelling-and-eventstorming-give-llms-the-context-they-need)
- **Bounded contexts reframed as "cognitive firewalls" / semantic safety boundaries.** Commentators argue DDD's insistence on precise, context-scoped language now doubles as safety engineering for AI components, confining models to domains where their understanding is valid and verifiable. (nidly.substack.com, 2025, https://nidly.substack.com/p/domain-driven-design-in-the-ai-era)
- **AI-powered DDD modeling tools emerged.** Tools such as Qlerify generate API-ready domain models, bounded contexts, and event-modeling artifacts from text prompts, lowering the barrier to first-draft context maps. (qlerify.com, 2025, https://www.qlerify.com/domain-driven-design-tool)
- **"Monolith first" / modular monolith remains the dominant default for context decomposition.** Industry guidance through 2025 continues to favor wide logical boundaries (modular monolith) over premature microservice splitting, reinforcing the file's wide-then-split sizing heuristic; microservice benefits are widely reported to appear mainly above ~10 developers. (javacodegeeks.com, "Microservices vs. Modular Monoliths in 2025", 2025, https://www.javacodegeeks.com/2025/12/microservices-vs-modular-monoliths-in-2025-when-each-approach-wins.html)
- **Core strategic concepts are stable.** Subdomain types, ubiquitous language, bounded contexts, and context-mapping patterns saw no fundamental revision in this period; change is in tooling and the AI-assisted workflow around them.
