# Modern Product Engineering

**Domain:** 01 - Foundations and SDLC Process
**Primary sources:** Engineering Software Products: An Introduction to Modern Software Engineering (Sommerville, 2020), chapters 1, 2, 3, 10; Software Engineering, Global Edition, 10th ed. (Sommerville, 2021), chapters 1-2 (reuse, SaaS).

## Overview
Modern software engineering increasingly means building software **products** rather than running software **projects**. A software product is a generic system sold to many customers (governments, businesses, consumers), ranging from millions of lines in enterprise systems to a few hundred in a mobile app. The engineering techniques evolved from 20th-century custom-software practice, but project-based techniques do not fit product development because the two start from fundamentally different places.

In **project-based** engineering, an external customer has a problem, generates requirements, enters a contract, and pays for the software and its changes; the customer controls the specification and the software typically has a long, supported lifetime. In **product-based** engineering, the developer identifies an opportunity, decides the product's features, chooses the platforms and release schedule, and controls changes; there is no external customer dictating requirements. Because development cost is spread across many customers, products are cheaper per customer, but buyers must adapt their ways of working to the software, and the developer can stop supporting it. Speed to market is critical: excellent products fail when an inferior product reaches customers first and they are reluctant to switch. This is why agile methods are universal for product development. The same product techniques apply to any development not driven by external-customer requirements: student projects, research software, and internal tools.

## Core concepts

**Software execution models** (how products are delivered and run):
- **Stand-alone** - all computation and data on the user's computer.
- **Hybrid** - some functionality on the user's device, some on the vendor's servers (many phone apps offload heavy processing).
- **Software as a service (SaaS)** - accessed over the internet from a browser or app, most computation on remote servers. Increasingly the default because it simplifies updating and enables new business models (pay-as-you-go). Sommerville's *Software Engineering* adds that SaaS shifted the economics of software: users pay per use or get free access funded by ads, software runs on shared clouds, and reuse of components/services became the dominant construction approach.

**Software product lines and platforms.** A *product line* is a set of products sharing a common core, each with customer-specific adaptations (used to deliver a custom-feeling system from a generic base). A *platform* provides functionality on which new applications can be built, within constraints set by the platform supplier (e.g., Facebook apps).

**Product vision.** A simple, succinct statement defining the essence of the product and how it differs from competitors; the basis for deriving features, and a yardstick to check that proposed features contribute to it. It should answer: *What* is the product and what makes it different? *Who* are the target users/customers? *Why* should they buy it? Geoffrey Moore's keyword template (from *Crossing the Chasm*): **FOR** (target customer) **WHO** (need/opportunity), **THE** (product name) is a (product category) **THAT** (key benefit, reason to buy), **UNLIKE** (primary competitive alternative), **OUR PRODUCT** (primary differentiation). Visions emerge from work and experimentation, drawing on domain experience, product experience, customer experience, and prototyping/"playing around" - not from a single "eureka moment."

## Frameworks & models

### Software product management
A business activity owning the product across its life, from conception through vision, implementation, and marketing, to withdrawal. Product managers (PMs) are the interface between the development team, the broader organization, and customers, and should be full members of the development team. PMs balance three interdependent concerns (Eriksson): **business needs** (meet the goals of the product company and its customers; plan releases with senior managers and marketing), **technology constraints** (make developers aware of technology issues affecting schedule/cost/functionality), and **customer experience** (understand what customers want, the types of user, and how the product is used; a critical input to UI design). PMs are generalists with technical and communication skills.

**Seven technical interactions of PMs with the development team:**
1. **Product vision management** - ideally lead vision development (though in startups the founders' idea often predates a PM), and police it against "vision drift" (gradual broadening that loses focus); assess proposed changes against the vision.
2. **Product roadmap development** - a plan for development, release, and marketing: goals, milestones (critical features, first test version), dates, success criteria, and a release schedule of features per release. Led by the PM but involving the team, company managers, and marketing.
3. **User story and scenario development** - refine the vision into features (PMs lead, grounded in customer knowledge, and validate stories/scenarios with real customers).
4. **Product backlog management** - the PM is the authority on backlog priority, helps refine broad items, and decides on re-prioritization.
5. **Acceptance testing** - verify a release meets the roadmap goals and is efficient and reliable; develop tests that reflect real customer use.
6. **Customer testing** - take a release to existing/potential customers for feedback on features, usability, and business fit; select and work with participating customers.
7. **User interface design** - act as a surrogate user, since developers are usually more technically skilled than users; evaluate UI features for unnecessary complexity.

### Product prototyping
The first thing to do in product development: build an early working version to test ideas, convince yourself and funders (venture capitalists usually insist on a prototype), and understand how to structure the final product. Aim for a demonstrable system in four to six weeks, cutting corners on reliability, performance, and UI. Often two stages: **feasibility demonstration** (prove the ideas work and beat competitors) and **customer demonstration** (extend the feasibility prototype to show potential customers). A prototype may also expose that the planned technology is inadequate, forcing a redesign of the product architecture.

### Persona / scenario / story / feature-driven development
A chain of narrative techniques for discovering and designing product features, from the user's perspective:

- **Personas** - character portraits of imagined user types (e.g., a teacher, a receptionist, a patient) that give the team a shared vision of users, helping it empathize and "step into the users' shoes." A persona covers four aspects: **personalization** (name, personal circumstances, optionally a stock photo), **job-related** (job and what it involves), **education** (educational background and technical skill, important for UI design), and **relevance** (why they might use the product and what they would do with it). Keep them short (two-three paragraphs); generally no more than five, and three-four scenarios per persona. Personas built on limited information are **proto-personas** - better than nothing, but cross-check against real user data when possible. (Sommerville deliberately omits "user goals," arguing most people lack clearly defined goals when using software.)

- **Scenarios** - narratives describing a situation in which a user uses the product's features to do something they want. Elements: a brief statement of the overall objective; references to the persona involved; what is involved in doing the activity; a problem the existing system cannot address; and one possible way the problem might be tackled. Scenarios facilitate communication and stimulate design creativity; they are understandable to users and funders. They are "tools for thinking," not specifications - they may be incomplete and inconsistent. Use **narrative** (not heavily structured) scenarios in early design, because users find structured scenarios intimidating; include concrete mechanisms (e.g., "log in with Google") when both users and developers understand them, but check whether such detail reflects a more general requirement. Asking users to write their own scenarios tends to fail (too detailed, too tied to how they work today).

- **User stories** - finer-grained narratives stating a single thing a user wants, in the standard format **"As a `<role>`, I want/need to `<do something>`"**, optionally **"...so that `<reason>`"** (a rationale helps developers unfamiliar with the domain and can trigger alternative ideas). A story too big for one sprint is an **epic** and should be broken down. Stories are used both to add detail to scenarios and to describe a feature's functionality; for planning they often populate the Scrum product backlog. Avoid **negative stories** ("I don't want...") in a backlog because you cannot write a test that demonstrates a negative; reframe them positively. Stories alone could express all functionality, but scenarios read more naturally, fit interviews with real users better, and carry more context.

- **Features** - the fundamental units of functionality, defined with an input/action/output template: the input data, the **action** (how the input is processed), the **activation** (how the user triggers it), and the **output** (to user and system). Feature-Driven Development (FDD) uses the template `<action> the <result> <by|for|of|to> <object>`. Utility features (Cut and Paste) need only a short description. Scenarios and stories are mined to derive the feature list.

### DevOps and continuous delivery
The ultimate goal of product development is to release to customers (app store, website download, or, increasingly, as a cloud service with no download). Traditionally separate development, release, and support teams introduced communication delays - even an urgent fix could take days. **DevOps** (development + operations) integrates development, deployment, and support in a single multi-skilled team. Three drivers: agile reduced development time but the release process became the bottleneck; Amazon re-engineered around services each developed and supported by one team (with reliability gains); and SaaS made it possible to release software as a cloud service rather than physical media.

**Three DevOps principles:** everyone is responsible for everything (joint responsibility for develop/deliver/support); everything that can be automated should be automated (minimal manual deployment); measure first, change later (collect data about the system and its operation, then use it to drive process/tool decisions). **Benefits:** faster deployment (fewer communication delays), reduced risk (small increments mean fewer interactions/outages), faster repair (the team fixes failures together rather than assigning blame), and more productive, happier, lower-turnover teams. DevOps needs the right **culture** - mutual respect and sharing, developers supporting the services they build, priority on fixing failures fast rather than assigning blame.

**Code management** (source code management, formerly software configuration management) underpins DevOps and predates it by ~40 years; essential regardless of DevOps. Without it, conflicting concurrent edits silently overwrite each other (Sommerville's Alice-and-Bob cautionary tale: a lost bug fix sinks the company). It supports four areas: **code transfer** (check out/in), **version storage and retrieval**, **merging and branching** (parallel development branches merged together), and **version information** (who changed what, when, why). Files get unique identifiers (never overwritten); every change carries an explanatory message. Git is the dominant tool (local repo cloned from a remote; `init`, `clone`, `add`, `commit`, `checkout -b` for branches, `merge`, `push`, `pull`; line-by-line merge with conflicts flagged for manual resolution). GitHub supports open-source workflows via personal + project repos and Webhooks (HTTP POST on repo events) to trigger builds and tests.

**DevOps automation** - "everything that can be should be automated," with automation encoded in versioned scripts/models so deployment does not depend on tacit knowledge:
- **Continuous integration (CI)** - every commit to the master branch triggers an automated build and test (gather elements, set up the database, load test data, compile, link, check external services, move config files, run system tests). Avoids the difficulty of isolating problems across infrequent, large integrations. Build tools (make, Ant, Maven, Rake) use dependency models and file timestamps to recompile only what changed; CI tools (Jenkins, Travis, Bamboo) define and run the pipeline.
- **Continuous delivery** - after changes, ensure the system is ready for delivery by testing it in a replica of the production environment (often a container built from the same image), including load and throughput/response-time tests. Does not mean immediate release; when to release is a business decision.
- **Continuous deployment** - a new release is deployed (as a cloud service) after every change to the master branch.
- **Infrastructure as code** - machine-readable models of the infrastructure (network, servers, routers) plus the software to install (compilers, libraries, DBMS) used by configuration-management tools to build the execution platform reproducibly.
- **Automated issue/bug tracking** (Bugzilla, FogBugz, JIRA) - issue reporting, searching/querying, data analysis (unresolved counts, resolution rate, dashboards), and integration with source code management; issues feed straight into the Scrum product backlog.

### Software reuse (Sommerville, *Software Engineering*)
Reuse is the dominant approach for building web-based and product software: assemble systems from preexisting components, services, and frameworks rather than writing from scratch. Reuse-oriented development relies on a base of reusable components plus an integrating framework (see the integration-and-configuration model in sdlc-and-process-models.md). Service-oriented engineering, where components are stand-alone web services, is a key enabler. Trade-off: lower cost and faster delivery, but requirements compromises and loss of control over the evolution of components you do not own.

## Patterns
- **Product vision statement** - *Intent:* anchor feature decisions in a concise, differentiated statement of what/who/why. *When:* the start of any product effort. *Trade-offs:* must be actively maintained against vision drift; a vague vision gives no real guidance.
- **Persona-driven empathy** - *Intent:* give the team a shared, concrete picture of user types to guide feature and UI decisions. *When:* you do not fully know your users, or the team holds divergent assumptions. *Trade-offs:* too many personas overlap and confuse; proto-personas built on guesswork can mislead.
- **Scenario-to-story-to-feature derivation** - *Intent:* move from natural narratives of use to backlog-ready stories and defined features. *When:* discovering and designing product functionality. *Trade-offs:* narratives are thinking tools, not specifications; over-structuring scenarios alienates users.
- **MVP / prototype-first** - *Intent:* validate desirability, feasibility, and market potential cheaply and fast (four-six weeks) before full build. *When:* novel products, funder buy-in needed, technology unproven. *Trade-offs:* corners cut on reliability/performance/UI; must not be shipped as production.
- **DevOps single team** - *Intent:* remove the dev/ops handoff bottleneck and speed deployment and repair. *When:* SaaS or frequently released products. *Trade-offs:* needs a no-blame, sharing culture and broad skill mix; cultural friction is the main risk.
- **CI/CD pipeline** - *Intent:* make integration, delivery, and deployment automated, reliable, and reproducible. *When:* incremental/agile product development. *Trade-offs:* up-front automation investment; continuous deployment demands strong automated testing and rollback discipline.
- **Software as a service** - *Intent:* deliver and update from the cloud, enabling new business models and instant updates. *When:* most modern products. *Trade-offs:* operational responsibility, availability/scaling concerns, dependence on connectivity.

## Principles & best practices
- Distinguish product from project: in products the developer owns the opportunity, features, platforms, schedule, and changes - there is no external customer generating requirements.
- Get the product to market quickly; an inferior product that ships first often wins.
- Use agile methods (Scrum, XP practices) for product development as the default.
- Start with a prototype; treat building a working demonstrator as the first task.
- Use personas, scenarios, and stories as tools for thinking, written from the user's perspective, validated with real users.
- Define features with input/action/activation/output so developers know what to build.
- Make the PM a full member of the team and balance business, technology, and customer-experience concerns.
- Automate everything in the delivery pipeline; encode automation in versioned, reviewable scripts and infrastructure-as-code.
- Use source code management with branching/merging and meaningful commit messages on every change, regardless of whether you adopt DevOps.
- Reuse aggressively (components, services, frameworks), accepting the requirements compromises that come with it.

## Anti-patterns & pitfalls
- **Applying project-based, requirements-and-contract-driven techniques to product development**, which does not fit because there is no external customer controlling the specification.
- **Vision drift** - letting the product vision gradually broaden until it loses focus.
- **Too many personas** that overlap and make it harder to design a coherent system; or **goal-centric personas** that try to pin down ill-defined "user goals."
- **Negative user stories in a backlog** - untestable; reframe positively.
- **Shipping the prototype as the production system** without re-engineering for reliability and performance.
- **No source code management** - concurrent edits silently overwrite each other and bugs reach production (the Alice-and-Bob failure).
- **DevOps without culture** - a single team in name only, with a "pecking order" of importance and blame, undermining the model.
- **Continuous deployment without strong automated testing** - small frequent releases amplify the risk of shipping undetected defects.

## Decision guidance
- If the developer owns the opportunity and decides features/schedule/platforms, engineer it as a product (agile, prototype-first, persona/scenario/story-driven), even for student projects, research software, or internal tools.
- Choose an execution model by update cadence and business model: SaaS when you want instant updates and pay-as-you-go and can own operations; hybrid when heavy processing must be offloaded but some local function is needed; stand-alone only when offline/local execution is required.
- Use a product line when many customers need a common core with specific adaptations; build on a platform when its functionality outweighs its constraints.
- Adopt full DevOps and continuous deployment when delivering SaaS; for app-store/download products, keep CI and automated testing but adapt the delivery steps.
- Invest in the prototype before committing build cost when desirability, feasibility, or technology is uncertain or when seeking funding.
- Reuse components/services to cut cost and time when suitable ones exist, but weigh the loss of control over their evolution.

## Source attribution
- Product vs. project engineering, execution models, product lines/platforms, product vision and Moore's template: Sommerville, *Engineering Software Products*, 2020, chapter 1.
- Software product management and the seven PM technical interactions, product prototyping: Sommerville, *Engineering Software Products*, chapter 1.
- Personas, scenarios, user stories, epics, and feature identification (input/action/output, FDD template): Sommerville, *Engineering Software Products*, chapter 3.
- Scrum product backlog, backlog grooming, sprints, story points/velocity, self-organizing teams (referenced for backlog/feature flow): Sommerville, *Engineering Software Products*, chapter 2 (see agile-foundations.md).
- DevOps principles/benefits/culture, code management and Git/GitHub, CI/CD/continuous deployment, infrastructure as code, issue tracking: Sommerville, *Engineering Software Products*, chapter 10.
- SaaS economics and software reuse as the dominant construction approach: Sommerville, *Software Engineering, Global Edition*, 10th ed., 2021, chapters 1-2 and reuse chapters.

## Recent developments (2024–2026)
Sommerville's product-engineering model (vision, personas, scenarios, stories, prototypes, DevOps, CI/CD, SaaS, reuse) holds up well, but AI has compressed several of its stages and added new automation and risk.

- **AI-accelerated prototyping and "vibe coding."** The four-to-six-week feasibility prototype Sommerville describes can now often be produced in days using AI app builders and agentic coding, lowering the cost of the prototype-first stance. The flip side is "vibe coding" (shipping AI output with minimal review), now a recognized anti-pattern that inflates technical debt and total cost of ownership; Kiro's tagline "from vibe coding to viable code" captures the corrective. (Baytech Consulting, 2025; Kiro, 2025)
- **Spec-driven development reshapes the story/feature flow.** Tools like GitHub Spec Kit and Amazon Kiro turn the persona/scenario/story chain into explicit, AI-consumable artifacts (requirements, design, tasks). User stories and acceptance criteria in EARS format become the structured prompt an agent builds against, making Sommerville's narrative techniques the front end of an AI pipeline rather than purely human planning tools. (GitHub Blog, 2025; Martin Fowler, 2025)
- **The PM role expands toward AI orchestration and data-backed decisions.** The 2025 State of Agile Report describes product owners becoming more strategic, data-driven decision makers, and developers becoming AI-augmented cross-functional contributors, broadening Sommerville's seven PM interactions to include curating AI-generated backlogs, prompts, and acceptance criteria, and validating AI output against the vision. (Digital.ai 18th State of Agile, 2025)
- **CI/CD pipelines gain AI stages and stronger guardrails.** Pipelines increasingly include AI code review, AI test generation, and security scanning as automated gates, reflecting evidence that AI-generated code carries ~2.74x more vulnerabilities and that larger AI changesets raise change-failure risk. This reinforces Sommerville's warning that continuous deployment demands strong automated testing and rollback discipline. (Veracode, 2025; DORA, 2025)
- **Reuse now includes models, services, and MCP-style integrations.** Reuse has extended from components and web services to foundation-model APIs, agent tools, and standardized model-context integrations, so "assemble rather than build" increasingly means composing AI capabilities, with the same trade-off Sommerville notes (faster delivery, less control over components you do not own, plus new dependency and data-governance concerns).
- **Platform engineering as the product delivery substrate.** Internal developer platforms reached near-universal adoption by 2025 (~90%), becoming the standard operational layer for SaaS product teams and a precondition for realizing AI's delivery benefits, extending the DevOps single-team idea toward self-service platforms. (DORA, 2025)
