# Frontend Architecture and UX Engineering

**Domain:** 14 - Information Architecture and UX
**Primary sources:** Angular for Enterprise Applications, 3rd ed. (Uluca, 2024); The React UX Architect's Handbook (Joshi, 2025)

## Overview

Frontend architecture is the discipline of structuring the client side of an application so it can grow in features, contributors, and complexity without collapsing under its own weight. Modern single-page applications (SPAs) are no longer thin views over a server: they hold state, manage navigation, fetch and cache data, render reactively, and must do all of this while staying fast, accessible, testable, and maintainable. The central architectural goal carried over from general software design is unchanged - low coupling and high cohesion - but it is applied to components, modules, routes, and state rather than services and databases. Uluca frames the "prime directive" plainly: keep the business logic behind the API decoupled from the presentation logic in the UI, because server-rendered, co-mingled logic is the root cause of expensive rewrites.

What distinguishes frontend architecture from backend architecture is that the user is in the loop. Performance is felt directly (a 100 ms latency cut Amazon's sales by 1%; 0.5 s of added Google search latency dropped traffic 20%), and structure must serve experience, not just engineering elegance. Both source books make this bridge explicit. Uluca argues "great UX should drive implementation" and uses router-first architecture to force teams to agree on navigation and screens before writing logic. Joshi grounds the entire React stack in design thinking, treating architecture decisions (component boundaries, state placement, system interfaces) as continuations of user research and problem framing. This file synthesizes both into framework-agnostic principles, noting Angular and React specifics where they diverge.

## Core concepts

**The decoupled client.** The lasting lesson of web history (Uluca) is that mixing presentation and business logic across layers makes UIs impossible to modernize without rewriting everything ("to replace the kitchen sink you must renovate the whole kitchen"). Decoupling the client from the server via a versioned API, and flattening/translating the data model at the API layer, prevents uncontrolled coupling between HTML views and relational schemas. GraphQL extends this by letting clients query exactly the data they need.

**Component as the atomic unit.** In both frameworks the component is the basic building block: a unit of UI that combines markup, styling, and logic with a clear contract. Angular components follow an MV* (MVC/MVVM hybrid) pattern where "binding" is the glue (ViewModel) between template and class; React components are functions taking props and returning UI. A well-designed component hides its implementation and exposes a clear interface - in React, typed props (TypeScript `interface ButtonProps`); in Angular, `@Input`/`@Output` and typed services injected via dependency injection.

**Props vs. state.** Joshi's fundamental distinction: props flow down from parent and are immutable within the child; state is owned internally and changes over time. Deciding what data lives where, and at what level, is one of the highest-leverage architectural choices in a component tree. The corollary is the smart/presentational (container/component) split: container components fetch data and hold state; presentational components only render from props, which maximizes reuse and testability.

**Reactive programming and data streams.** Angular is reactive "through and through" via RxJS: everything is treated as an asynchronous stream that can be transformed (`map`), filtered (`filter`), and throttled (`throttle`) with composable, stateless operators. This contrasts with imperative step-by-step code and supports testability because each operator is a pure, composable unit. Angular Signals (from v16/17) add fine-grained reactivity using native ES2022 features, updating only the DOM that changed and avoiding RxJS's "leaky subscription" memory pitfalls. React's analog is hooks (`useState`, `useEffect`, `useMemo`, `useCallback`) and its render-on-state-change model.

**Routing as architecture.** The router is not a navigation afterthought but the backbone of a scalable SPA. It defines which code loads when (lazy loading / code splitting), enables deep-linking and browser-native navigation, and lets large teams own separate route-bounded modules without colliding. Both books treat route boundaries as primary system boundaries.

**State management as a spectrum.** State ranges from local component state, through shared/derived state, to global application state and server (cache) state. The modern consensus in both ecosystems is to use the lightest tool that fits and to recognize that server state is fundamentally different from client state (Joshi). Over-adopting a heavy global store is a common anti-pattern.

**System boundaries and interfaces (systems thinking).** Joshi's systems-thinking chapter reframes components, services, and state partitions as boundaries that should align with business domains and user workflows, not technical convenience. Stable interfaces (typed contracts, service layers that hide whether data comes from REST, GraphQL, or cache) let parts evolve independently and let teams work in parallel while reducing cognitive load.

## Frameworks & models

**Router-first architecture (Uluca).** A seven-step method for line-of-business (LOB) apps:
1. Develop a roadmap and scope (backlog, wireframes, prototypes) before coding.
2. Design with lazy loading in mind - divide the app into feature modules by business function or user role.
3. Implement a walking-skeleton navigation experience (all routes stubbed early).
4. Achieve a stateless, data-driven design (components hold references to data, not bookkeeping state).
5. Enforce a decoupled component architecture.
6. Differentiate user controls (interaction widgets) from components (business views).
7. Maximize code reuse with TypeScript and ES features.
The aim is to maintain *optimal engineering overhead* - enough structure to scale, not so much that you over-engineer - and to force consensus on screens and navigation up front, which is where late-game change is most expensive.

**The 80-20 (Pareto) solution (Uluca).** Most working software is LOB apps, not billion-user systems. Architect for the 80% of value delivered by 20% of effort: keep engineering overhead minimal, avoid experimenting in production code, and pick an architecture flexible enough to absorb change but not gold-plated. Under-engineering (typical of small/passion projects) and over-engineering (typical of enterprise efforts) both hit an inflection point where the architecture becomes inadequate; the goal is to ride the middle.

**Atomic Design (Brad Frost, applied to React by Joshi).** A five-level hierarchy for building UIs as systems rather than pages: atoms (button, input, label) -> molecules (search form = input + button) -> organisms (nav bar) -> templates (page structure without content) -> pages (real content). It maps cleanly to a React folder structure (`/components/atoms`, `/molecules`, ...) and creates a shared design-development vocabulary that bridges the handoff gap.

**Flux / unidirectional data flow.** Facebook's Flux keeps application state in a store mutated only through dispatched actions and read through well-defined selectors/views, producing decoupled, testable code. Angular's NgRx and React's Redux both implement it. Both authors caution it is overkill for most apps: Uluca recommends NgRx only for 3+ input streams or offline-first PWAs needing to persist/restore complex state, defaulting to RxJS `BehaviorSubject`; Joshi documents the post-Redux shift to lighter tools.

**Three-tier and modern web architecture (Uluca).** Presentation / business / persistence layers, expanded in modern apps with an API layer (transforms/transfers data), a tools-and-practices layer, and a testing layer. Useful as a mental map of where logic should and should not live.

**Design thinking as the architecture driver (Joshi).** The five stages - empathize, define, ideate, prototype, test - feed directly into architecture: user research and problem framing determine component boundaries and information architecture; prototyping (Storybook, Figma) de-risks high-uncertainty areas; testing validates before committing to structure. Architecture is treated as a continuation of UX, not a separate phase.

## Patterns

- **Router-first architecture** - Intent: design navigation, routes, and lazy-load boundaries before business logic so teams agree on screens up front and the app scales by feature. When to use: line-of-business and enterprise SPAs (Uluca's primary recommendation for Angular). Trade-offs: requires discipline and early roadmap/wireframe work; pays off by avoiding costly late-stage layout/navigation rework and enabling parallel team ownership.

- **Feature module / feature-folder structure** - Intent: group related components, services, hooks, and routes by business function (not by file type) into independently loadable units. When to use: any app expected to grow beyond trivial size. Trade-offs: more up-front organization and explicit imports (especially with Angular standalone components); delivers low coupling, high cohesion, lazy-loadable bundles, and easier navigation. (Angular: feature `NgModule` or standalone route config; React: `/features/<name>/{components,hooks,services}`.)

- **Lazy loading / code splitting** - Intent: defer downloading non-critical code until a route or component is needed, minimizing initial bundle and time-to-interactive. When to use: any app where first paint matters (53% of mobile users abandon at >3 s load). Trade-offs: slight transition delay when entering a new area (mitigable with preloading strategies); essential for scalable performance and hard/costly to retrofit later. (Angular Router lazy routes; React `lazy` + `Suspense`, `react-window` virtualization.)

- **Smart / presentational (container / component) split** - Intent: separate components that fetch data and hold state from components that only render from props. When to use: whenever a view has both data-fetching and rendering concerns. Trade-offs: more components to wire; yields reuse, testability, and a clean data flow.

- **Stateless, data-driven component** - Intent: components hold references to data and allow access/mutation but store no bookkeeping/state in instance variables. When to use: router-first Angular apps; broadly to reduce state bugs. Trade-offs: requires a shared data anchor (e.g. readonly `BehaviorSubject` in a service); greatly improves predictability and testability. ("State is evil" - Uluca.)

- **Subject-in-a-service / data anchor (BehaviorSubject)** - Intent: share data across components via a reference held in a singleton service exposed as an observable, synchronizing views without a global store. When to use: most Angular apps with REST + user-input streams. Trade-offs: lighter than NgRx but you manage subscriptions/memory; sufficient for the large majority of apps.

- **Context + reducer (scoped state)** - Intent: manage shared state without external libraries by pairing React Context with `useReducer`, splitting state and dispatch contexts to limit re-renders. When to use: medium-complexity cross-component state. Trade-offs: raw Context causes unnecessary re-renders if not split; no devtools/middleware out of the box.

- **Atomic state (Jotai / Recoil)** - Intent: define each piece of state as an independent atom that components subscribe to individually, with derived atoms for computed state. When to use: apps needing fine-grained reactivity and minimal re-renders. Trade-offs: another mental model and dependency; excellent render performance and composability.

- **Lightweight global store (Zustand)** - Intent: Redux-like global state with a tiny API, selector-based subscriptions, and middleware (persistence, immer, devtools) without provider wrappers. When to use: teams wanting Redux predictability without boilerplate. Trade-offs: less ecosystem/convention than Redux; flat learning curve.

- **Server-state cache (React Query / SWR)** - Intent: manage remote data separately from client state with automatic caching, background refresh, and stale-data handling. When to use: any app with meaningful API data. Trade-offs: a dedicated layer to learn; removes large classes of manual fetching/caching bugs and recognizes server state's distinct lifecycle.

- **Atomic Design hierarchy** - Intent: compose UIs from atoms -> molecules -> organisms -> templates -> pages to build a consistent design system rather than disconnected screens. When to use: design-system-driven products and design/dev collaboration. Trade-offs: requires up-front taxonomy discipline; yields consistency, reuse, and a shared vocabulary.

- **Design-system component library** - Intent: a reusable, documented, versioned set of UI components (Storybook stories, typed props, accessibility notes) as the single source of UI truth. When to use: multi-team or multi-product organizations. Trade-offs: ongoing maintenance and governance; "pick a component library and stick to it, but avoid vendor lock-in" (Uluca).

- **Service-boundary / API client object** - Intent: hide protocol details behind a stable service interface so consumers do not know whether data comes from REST, GraphQL, or cache. When to use: any non-trivial app integrating external systems. Trade-offs: an abstraction layer to maintain; lets backends evolve without breaking the UI and centralizes auth/error handling.

- **Flux / unidirectional store (NgRx, Redux)** - Intent: single source of truth mutated only via actions and read via selectors, isolating side effects in effects/middleware. When to use: 3+ input streams, offline-first PWAs, or complex shared state needing persist/restore. Trade-offs: significant boilerplate and overhead; not the default - prefer lighter patterns until the complexity is real.

- **Error boundary / graceful failure** - Intent: contain rendering failures to a subtree and show a fallback instead of crashing the app. When to use: around risky or data-dependent UI regions. Trade-offs: extra wrapping components; materially better resilience and UX.

## Principles & best practices

- **Low coupling, high cohesion - applied to the UI.** Group related logic together (a domain's components/services in one module); keep unrelated pieces independently replaceable. Andy Hunt's Law of Design: "If you can't rip every piece out easily, the design sucks." (Uluca)
- **Great UX should drive implementation.** Architecture decisions follow from user needs and the experience you intend to deliver, not the other way around. (Both)
- **Keep state minimal and intentional.** Prefer stateless, data-driven design; push state as low as possible; treat introducing global state as a deliberate decision. (Both)
- **Verbosity over terseness for maintainability.** "You will read the code hundreds of times but write it a few" - prefer explicit, decoupled, encapsulated code; configuration over convention reduces magic and debugging cost. (Uluca)
- **Performance is a UX feature with revenue impact.** Target smooth rendering (Uluca cites a 60 FPS goal and ~340 ms click response), sub-3 s first paint, small bundles via lazy loading; optimize re-renders with memoization (`React.memo`, `useMemo`, `useCallback`), virtualization for long lists, and fine-grained reactivity (Signals). (Both)
- **Define stable, typed contracts.** Use TypeScript interfaces/props to document and enforce how components and services are used; this is documentation that the compiler checks. (Both)
- **Organize by feature, not by type.** Improves discoverability, ownership, and lazy-loading boundaries. (Joshi; consistent with Uluca's feature modules)
- **Test behavior, not implementation.** For components, assert observable behavior (React Testing Library, Cypress e2e); favor TDD/CI/CD as Agile engineering practices. (Both)
- **Build accessibility in, not on.** Treat accessibility as a first-class architectural concern (semantic markup, ARIA, keyboard, contrast); both ecosystems provide tooling and Angular ships accessibility tooling as a selling point for enterprise. (Both)
- **Adhere to SOLID and DRY with judgment.** Single responsibility per component/service; depend on abstractions (dependency injection); reduce duplication sensibly without over-DRYing into rigidity. (Uluca)
- **Stay evergreen.** Upgrade dependencies proactively and incrementally rather than accumulating a risky rewrite; frameworks with deterministic releases (Angular's 6-month cadence, 18-month support) make this plannable. (Uluca)
- **Right-size the architecture (80-20).** Match engineering overhead to app size, purpose, team skill, and delivery cadence; avoid both under- and over-engineering. (Uluca)
- **Component reusability heuristic.** "Can this component be used in at least three different places without modification?" If not, reconsider its boundaries. (Joshi)

## Anti-patterns & pitfalls

- **Co-mingled presentation and business logic** (server-side MVC drift, logic spread across layers): the classic cause of multimillion-dollar rewrites; UI modernization becomes impossible. (Uluca)
- **State sprawl in singleton services.** Angular services are singletons by default; storing mutable state in them silently introduces hard-to-trace global state. Provide app-wide services only at the root and avoid bookkeeping in them. (Uluca)
- **Reaching for a heavy global store too early.** Redux/NgRx boilerplate for apps with only REST + user-input streams; prefer Context+reducer, atomic state, Zustand, or BehaviorSubject. (Both)
- **Raw Context causing re-render storms.** Using a single Context for everything re-renders all consumers; split state/dispatch contexts and subscribe narrowly. (Joshi)
- **Retrofitting lazy loading.** Cheap to design in, costly to add later; not planning route/module boundaries up front forces re-architecture. (Uluca)
- **Wrong component granularity.** Too fine-grained creates needless complexity; too coarse-grained becomes inflexible and unmaintainable - anchor on single responsibility. (Joshi)
- **Leaky abstractions / exposed internals.** Components or services that leak implementation details create coupling and brittle systems; expose minimal, stable interfaces. (Joshi)
- **Leaky RxJS subscriptions.** Forgetting to manage/unsubscribe causes memory leaks; Signals mitigate this, and disciplined subscription management is required where RxJS is used. (Uluca)
- **Adopting preview/unfinished framework features in production.** Uluca warns that the pressure for splashy releases ships features in preview (e.g. early Signals, Angular Elements), risking performance regressions; vet before shipping.
- **Mixed coding paradigms in one codebase.** Angular's flexibility (imperative vs reactive, OOP vs functional, single- vs multi-file components) becomes a weakness without team norms; linting cannot catch paradigm drift. (Uluca)
- **Process over working software.** "Don't be a process monkey" - moving cards and counting story points does not ship software; focus on delivering working code. (Uluca)
- **Premature tooling.** Don't start with Photoshop when paper and pencil will do; don't add npm packages or libraries until justified. (Uluca)

## Decision guidance

- **Choosing a framework.** Angular favors configuration-over-convention, batteries-included tooling, DI, native TypeScript, deterministic releases, and accessibility tooling - strong for large enterprise/LOB apps with diverse teams and 1,000+ views (Uluca). React favors a lighter library core with a flexible, fast-evolving ecosystem and excels where design-thinking-driven, component-and-design-system-centric workflows dominate (Joshi). For simple/CMS-style Angular use cases, consider a meta-framework (Analog). Match the choice to team skill, app scale, and longevity needs.

- **Choosing a state solution (decision ladder).** Start with local component state -> lift state up / props -> Context (split state/dispatch) or BehaviorSubject for shared state -> atomic state (Jotai/Recoil) or Zustand for app-wide client state with good performance -> dedicated server-state cache (React Query/SWR) for remote data -> full Flux store (NgRx/Redux) only when you have 3+ input streams, offline-first persistence/restore needs, or genuinely complex cross-cutting state. Always separate server state from client state.

- **Where to place a boundary.** Align component, module, route, and service boundaries with business domains and user workflows (e-commerce: browsing, cart, checkout, profile), not with technical layers. Boundaries that match the user's mental model produce the most intuitive and maintainable systems. (Joshi)

- **How much architecture to invest (80-20).** Weigh app size, why you're building it, developer skill levels, delivery cadence, and the forward flow of features. Aim for optimal engineering overhead: extensible enough to pivot without large rewrites, simple enough not to drown the team. Use router-first as the structural baseline for LOB apps. (Uluca)

- **Performance triage.** Budget first paint and bundle size (lazy load by feature/role); then optimize render hot paths (memoization, `useCallback`, virtualization, Signals/fine-grained reactivity, SSR with non-destructive hydration). Measure against concrete targets (FPS, click latency, load time) because the cost of slowness is measured in revenue.

- **Bridging design and engineering.** Treat the design system / component library as the shared contract between designers and developers; use Atomic Design as the organizing taxonomy and Storybook for component-level prototyping, documentation, and review. Prototype the highest-risk areas first. Let user research and problem framing (design thinking) drive component boundaries and information architecture rather than retrofitting UX onto a fixed structure.

## Source attribution

- **History, decoupling, three-tier/modern web architecture, MV*/component anatomy, DI, reactive programming/RxJS/Signals, modular vs standalone architecture, Angular Router and lazy loading, state management (BehaviorSubject, Flux, NgRx, component store), framework comparison, future of Angular:** *Angular for Enterprise Applications, 3rd ed.* (Uluca, 2024), Ch. 1.
- **Technical-lead/architect role, elastic leadership, Agile engineering best practices, SOLID/DRY, enterprise suitability, high-performance tooling and runtime-performance targets, the 80-20 solution, router-first architecture and its seven steps, feature modules, designing with lazy loading:** *Angular for Enterprise Applications, 3rd ed.* (Uluca, 2024), Ch. 3-4.
- **Design thinking as architecture driver, user-centered architecture, component-based architecture principles (contracts, props/state, pure components, smart/presentational, communication patterns, granularity, folder structure, naming, docs, testing, perf), state management approaches in 2025 (Context+reducer, Jotai/Recoil, Zustand, React Query/SWR), Atomic Design, Storybook/Figma prototyping, systems thinking (boundaries, interfaces, service layers, managing complexity, resilience):** *The React UX Architect's Handbook* (Joshi, 2025).

## Recent developments (2024-2026)
The core principles (decoupled client, low coupling/high cohesion, routing as architecture, state spectrum, feature-folder structure) are stable. The frameworks converged on reactivity and server collaboration since the 2024-2025 sources.

- **React 19 ships and React Server Components (RSC) go mainstream.** RSC enforce a strict server/client split (browser-API, stateful, and lifecycle code marked "use client"), shifting work to the server for performance. Next.js is the only framework with full production-ready RSC support as of 2025; teams report real benefits but also new pitfalls (data-fetching boundaries, hydration cost). (Growin, "React Server Components in Production," 2026; developersvoice.com, 2025.)
- **Signals become the shared reactivity model.** Angular adopted Signals as a built-in, pull-based reactive state solution (stable through Angular 17-19), updating only the DOM that changed and reducing reliance on RxJS subscriptions; the React ecosystem is moving the same direction with signal-style libraries. The file's Signals coverage is now the default, not a preview feature. (nx.dev, "Angular State Management for 2025"; dev.to, "Signals in React 2025"; 10xdev.blog, "Angular 19," 2025.)
- **Framework convergence over framework choice.** The 2025 consensus is that React, Angular, and Vue now sit on one continuum of rendering, hydration, and server collaboration; fluency in Signals, Server Components, and reactive thinking matters more than picking a framework. This supports the file's framework-agnostic stance. (developersvoice.com, "React vs Vue vs Angular for Enterprise Architects," 2025.)
- **AI coding tools make design tokens and structure non-negotiable.** Without design tokens, AI-generated UI code is inconsistent by default; teams adopt token systems partly to constrain AI output, reinforcing the design-system-as-contract pattern. (oneminutebranding.com, "Design Tokens in 2026"; designsystemscollective.com, 2025.)
- **Modularity and performance discipline persist.** 2025-2026 frontend-architecture writing continues to emphasize modular boundaries, lazy loading/code splitting, and measured performance budgets as the scalability fundamentals, consistent with router-first and 80-20 guidance. (blog.madrigan.com, "Frontend Architectures 2025," 2025-2026.)
