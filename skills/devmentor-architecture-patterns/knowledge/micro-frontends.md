# Micro Frontends

**Domain:** 05 - Architecture Patterns and Styles
**Primary sources:** Micro Frontends Architecture for Scalable Applications (da Silva, 2025)

## Overview
Micro frontends apply the principles of backend microservices to the frontend, splitting a user interface into small, loosely coupled, independently developable and deployable pieces owned by autonomous teams. The architecture emerged to solve the complexity of monolithic frontends in large organizations where multiple teams with diverse skills need to work without bottlenecking on a shared codebase. Rather than replacing patterns like SPA, SSR, or Jamstack, micro frontends are a "super-exceeding" architecture: within the boundaries of each micro frontend, teams can use whatever frontend architecture and framework best fits their domain, as long as all pieces talk to each other through well-defined public contracts.

This topic matters in the SDLC because frontend scaling is increasingly an organizational problem as much as a technical one. Micro frontends offer team autonomy, independent deployment, technology flexibility, and improved maintainability, but they introduce real costs: integration complexity, performance overhead, dependency duplication, coordination overhead, and harder routing and shared state. The architecture is most valuable for large, complex, scalable applications with multiple teams, and is overkill for small applications or single-team frontends.

## Core concepts

### The two pillars: domains and public contracts
- **Domains (slicing)** - the boundary definitions of each micro frontend: what is in and what is out. Ideally each micro frontend is the frontend implementation of a DDD bounded context (a core, supporting, or generic subdomain).
- **Public contracts (integration strategies)** - the language in which the different micro frontends talk to each other and to the shell. Contracts should be versioned and backward-compatible whenever possible.

### Foundational principles (Ch 4)
- **Domain-Driven Design** - create a shared understanding of the problem domain so micro frontends align with business capabilities, not UI routes.
- **Decentralized governance** - empower teams with autonomy and faster decision-making.
- **Independent deployments** - isolate features so each can be developed, tested, and deployed on its own cadence.
- **High observability** - teams can monitor and manage their pieces effectively.
These are values to uphold, not rigid constraints; deviations should be deliberately monitored.

### The shell application (container)
The shell aggregates the different micro frontends onto a single page, composing them in server, edge, or client contexts depending on the integration strategy. Its goal is "to serve as a stable and predictable platform on which different micro frontends can be built." Two facets of stability: public-contract stability (minimize the frequency and breaking nature of contract changes; delegate responsibilities to micro frontends so the shell changes less) and resource isolation (one micro frontend's failure or performance issue must not take down the whole application). Common shell responsibilities: state management, routing, cross-application communication, shared dependencies, bootstrapping/initialization, layout and UI composition, authentication/authorization, error handling/resilience, lazy loading/performance, and feature flags/A-B testing. A system may have multiple shells (for example, one per device) referencing the same micro frontends.

## Frameworks & models

### Slicing strategies
- **Per feature slicing** - split by business capability; each micro frontend reflects one domain (aligns with DDD and microservice boundaries). Multiple micro frontends can run on one page. Best for cohesion and reuse, but raises integration, shared-state, and performance challenges. Preferred when possible.
- **Per page slicing** - each route is an independent micro frontend, so only one is active per page. A purely technical strategy that sidesteps integration challenges, but misaligns with DDD, ties ownership to UI routes rather than domains, and increases the need to share common structures (header, search). Useful as a migration step or for isolated features.
- Both can coexist. Note: navigation and header are reusable components, not micro frontends, because they carry no business-domain knowledge.

### Slicing decision flow
Does the organization adhere to DDD? If yes, reflect bounded contexts as micro frontends (per feature). If no, has it adopted microservices (even partially)? If yes, leverage those service boundaries (per feature). If no, is it a greenfield app? If yes, apply DDD from the start (per feature). Otherwise (legacy monolith, no DDD/microservices), use per page slicing to bootstrap isolated changes.

### Composition / integration strategies
- **Server-side template composition** - fragments rendered by separate services are assembled into one HTML document by a server-side application shell (using template engines). Trade-offs: the "slowest link" problem (total response bound by the slowest fragment), reduced resilience (a failed fragment can break the page without fallbacks), the composition server as a bottleneck, tricky caching (per-fragment or ESI needed), and global latency/TTFB. Mitigate with timeouts and fallback behavior per fragment.
- **Edge-side composition** - composition pushed to the CDN/edge (for example, Edge Side Includes) to improve caching and latency.
- **Client-side composition** with several run-time integration options:
  - **iFrames** - highest isolation (sandboxed content, JS, CSS), good for security-sensitive embeds (payment, auth) and third-party tools. Drawbacks: performance/memory overhead, cumbersome postMessage-only communication, SEO/accessibility issues, styling/layout constraints, cross-origin/CORS complexity, inconsistent UX.
  - **JavaScript (script tags / ES Modules)** - full control and flexibility with native features; you own the orchestration. Single-SPA and similar frameworks (Piral, Luigi, Qiankun, FrintJS, Mosaic) ease integration with lean APIs but risk framework lock-in and costly later changes; each micro frontend implements mount/unmount lifecycle methods.
  - **Web Components** - partial isolation via the Web Component spec (Custom Elements, Shadow DOM for CSS/markup encapsulation, HTML Templates, Slots). Each micro frontend registers a custom tag as its contract; flexible data exchange (not limited to postMessage); good cross-framework interoperability; but JavaScript shares the global context, so an error can crash the whole app.
  - **Module Federation** - introduced in Webpack 5 (2020) for run-time code sharing between separate builds; each micro frontend is a separate build that exposes and consumes components at run-time, with powerful shared-dependency handling that reduces bundle size. Module Federation 2.0 decoupled it from Webpack, allowing other bundlers. Acts as a public contract specifically tailored to micro frontends.
- **Client-side composition for native apps** - native modules via JavaScript bridge, and React Native Module Federation.
- **Build-time integration (package manager)** - micro frontends consumed as packages (npm, Gradle, Swift PM). Considered an anti-pattern because it breaks independent deployment, but acceptable where a controlled, centralized release is mandatory; simpler, more predictable, with shared libraries bundled once.

### Cross-application communication models (by coupling)
- **Shell-orchestrated** (frontends unaware of each other) - micro frontends emit outputs to the shell; the shell orchestrates inputs/outputs and state. Loosest from the micro frontend's perspective.
- **Event-based** (frontends suspect others exist) - publish/subscribe via CustomEvent API, shared messaging services, or postMessage. Encourages loose coupling.
- **Direct** (frontends expect others exist) - direct calls via well-known interfaces (commands/queries). Tighter coupling; avoid unless the simplicity gain clearly outweighs maintainability risk.

### Routing strategies
- **Single global router** - one router at the shell level controls the URL structure and exposes a public contract; micro frontends navigate through it and handle their own internal routes. Centralizes control and checks.
- **Independent routing per micro frontend** - each manages its own URL structure; the shell may not know internal routes. Requires URL namespaces to avoid conflicts in per-feature pages; per-page slicing makes the route itself the isolation boundary.
- **Server/edge-side routing** - the server/edge interprets the request URL and assembles the right micro frontends, via URL-to-component mapping, content negotiation/personalization, caching/pre-composition, and shared or split routing responsibility.

## Patterns
- **Shell / container application** - Intent: a stable, predictable platform that composes micro frontends and owns cross-cutting concerns. When to use: any non-trivial micro frontend system, especially per-feature (horizontal) slicing. Trade-offs: can accumulate too many responsibilities and become a coupling point; keep its public contract small and delegate to micro frontends.
- **Per-feature slicing (domain-aligned)** - Intent: one micro frontend per bounded context. When to use: DDD/microservice-aligned orgs seeking team autonomy and cohesion. Trade-offs: multiple micro frontends per page raise integration, shared-state, and performance challenges.
- **Per-page slicing** - Intent: one micro frontend per route, one active at a time. When to use: migration from a monolith, isolated features, orgs without DDD/microservices. Trade-offs: misaligns ownership with domains; duplicates shared structures.
- **Module Federation as public contract** - Intent: run-time sharing/exposing of components and dependencies across independent builds. When to use: client-side run-time integration where shared dependencies and bundle size matter; teams agree on it as the standard. Trade-offs: historically Webpack-coupled (eased by MF 2.0); requires shared understanding across teams.
- **Web Components as public contract** - Intent: standardized custom-element interface per micro frontend. When to use: cross-framework interoperability with partial CSS isolation. Trade-offs: shared JS global context risks app-wide crashes.
- **iFrame isolation** - Intent: maximal sandboxing of an embedded micro frontend. When to use: security-sensitive or untrusted third-party content. Trade-offs: performance, communication, SEO/accessibility, and UX costs.
- **Shared messaging service (pub/sub anti-corruption layer)** - Intent: a singleton subscribe/publish broker abstracting environment details (namespaces, security, non-browser targets) over native CustomEvent/postMessage. When to use: event-based communication beyond a single window context or needing grouping/security. Trade-offs: a shared dependency to distribute (npm/Module Federation).
- **Selective state exposure (public type)** - Intent: expose only the minimal fields a micro frontend's bounded context owns (a PublicTask projected from an internal Task) rather than internal models. When to use: any shared state, since exposed state becomes a public contract. Trade-offs: extra mapping; protects maintainability and backward compatibility.
- **Single global router** - Intent: centralize URL control behind a shell contract while micro frontends own internal routes. When to use: per-feature pages needing consistent navigation and checks. Trade-offs: shell coupling point.
- **Shared dependencies (Module Federation / CDN / import maps)** - Intent: a common set of library versions to eliminate conflicts and reduce bundle size. When to use: only when real performance or consistency problems appear (YAGNI). Trade-offs: less independence, resilience, and autonomy; use judiciously as a last resort. Module Federation's declarative sharing with graceful fallback is the most robust; CDN links and import maps are bundler-independent but offer fewer guarantees.

## Principles & best practices
- Align micro frontends with bounded contexts; let business domains, not UI routes, drive ownership.
- Keep the shell's public contract small and stable; delegate responsibilities to micro frontends to reduce shell change frequency.
- Keep state private by default; expose only what the bounded context owns, and project a minimal public type when you must expose it - exposed state is a contract requiring backward compatibility.
- Prefer the loosest viable communication: shell-orchestrated or event-based over direct calls.
- Treat shell-owned globals (window, localStorage, IndexedDB, document, navigation) as the shell's responsibility to keep behavior predictable and environment-agnostic (server-rendering-friendly).
- Favor independence, autonomy, and resilience over premature sharing; introduce shared dependencies only to fix real, observed performance or consistency issues (YAGNI). When sharing, prefer Module Federation's declarative model with fallback.
- Design for resilience: timeouts, fallbacks, graceful degradation, and error containment per fragment/micro frontend so one failure does not break the page.
- Unify UX through a design system (see below) layered over autonomy.

### Design system and governance
A design system (not to be confused with system design) is a framework of principles, guidelines, and reusable resources for consistent, scalable UIs: design tokens (colors, typography, spacing, motion), semantic palettes, text styles, spacing scales, responsive grids, animation rules; reusable components organized by complexity; reusable interaction patterns; accessibility and branding standards; centralized documentation; and code libraries for cross-platform implementation. It follows **Atomic Design** (Brad Frost): atoms (buttons, inputs, icons) -> molecules (a search bar) -> organisms (headers, cards) -> templates (page skeletons) -> pages (templates with real content). In micro frontends the design system is the centralized framework that bridges team independence and visual/interaction cohesion, ensuring a consistent experience regardless of which team or stack built a module. Tokens and components are cross-disciplinary artifacts shared by product, UI/UX, and development.

## Anti-patterns & pitfalls
- Using micro frontends for small/simple applications or single-team frontends - the integration, deployment, and coordination overhead outweighs the benefits; a monolithic or modular frontend is easier.
- Build-time integration as the default - it breaks independent deployment, the core principle of the architecture (acceptable only when centralized release is genuinely mandatory).
- Dependency duplication: teams shipping different versions of the same library, bloating bundles and complicating versioning.
- Performance overhead from loading many bundles/assets and duplicated dependencies without lazy loading, shared deps, and caching.
- Coordination overhead and route/state conflicts when protocols for routing, auth, and global state are not agreed up front.
- Exposing rich internal state as a public contract, then being forced to manage breaking changes forever.
- Treating navigation/header as micro frontends rather than reusable components.
- Global-namespace event collisions and security leaks when using raw CustomEvent without namespacing or origin checks (any script can listen, for example, to "userLoggedIn").
- Premature/speculative shared dependencies that sacrifice autonomy and resilience for efficiency that was never needed.

## Decision guidance
- Adopt micro frontends only for large, complex, scalable applications with multiple autonomous teams and organizational scale; otherwise prefer a modular monolith frontend.
- Slice per feature when DDD or microservices give you boundaries (or in greenfield where you can apply DDD); slice per page to migrate a legacy monolith or ship isolated features.
- Choose composition by context and isolation needs: server/edge-side for SEO/initial-load and CDN caching; client-side run-time (Module Federation, Web Components, JS) for rich interactivity and team autonomy; iFrames only for strict isolation/security; build-time only when a controlled central release is required.
- Pick a public contract (Module Federation, Web Components, or a documented JS/event interface) and standardize it across teams.
- Choose routing by composition context and coupling tolerance: single global router for consistent navigation and central checks; independent routing for autonomy (with URL namespacing); server/edge routing when composing on the server.
- Default to maximal independence and the loosest communication; trade it away for shared dependencies and consistency only when real metrics demand it. Always invest in a design system to keep UX cohesive across autonomous teams.

## Source attribution
- Frontend architectural patterns context, micro frontend origins/principles/advantages/challenges/when-not-to-use: da Silva (2025), Ch 3.
- Foundational principles (DDD, decentralized governance, independent deployments, observability): Ch 4.
- Integration/composition strategies (slicing, shell, server-side/edge-side/client-side composition, iFrames, JS/Single-SPA, Web Components, Module Federation, native, build-time): Ch 5.
- Communication and routing (shell responsibilities, state management, communication models, routing strategies, shared dependencies): Ch 6.
- Design system and Atomic Design, UI/UX consistency: Ch 7.

## Recent developments (2024–2026)

- **The hype cooled and the discourse turned pragmatic.** After dominating conference talks around 2021, micro frontends became quieter by 2025. The framing shifted from "scale your frontend with autonomous teams" to "what problem are we solving, and is this the right tool?" The mainstream 2025 verdict: worth it primarily when the binding constraint is organizational scale (many teams shipping independently across loosely coupled domains with real platform-team investment); otherwise a modular monolith frontend is the better default. This matches the file's existing decision guidance. (Feature-Sliced Design 2025; DEV Community/tahamjp 2025; devtechinsights.com 2025)
- **Module Federation decoupled from Webpack and went multi-bundler.** Module Federation 2.0 now works across Webpack, Vite, Rollup, and Rspack, and is treated as production-grade rather than experimental. Nx, Turborepo, and Vite ship built-in support; the file's note that MF 2.0 "decoupled it from Webpack" is now fully realized in the tooling ecosystem. (10x.pub 2025; freeCodeCamp; DEV Community 2025)
- **Server-side Module Federation was the notable 2025 advance.** It lets servers stitch together remote server components from different origins into a single streamed response, extending federation into SSR/React Server Components territory. This is a genuinely new composition capability beyond the client-side options catalogued in the file. (blog.weskill.org 2026)
- **Native ESM federation via import maps is the 2026 direction.** Momentum is shifting toward browser-native ESM federation using import maps and native module loading, reducing build overhead versus bundler-coupled federation. This reinforces the file's mention of import maps as a bundler-independent sharing mechanism, now with first-class framework support. (blog.weskill.org 2026; Refonte Learning 2026)
- **Framework-native multi-zone support reduced the need for bespoke shells.** Modern hosts (Next.js multi-zones, Vite-based setups) increasingly offer built-in patterns for composing independently deployed frontend zones, lowering the integration tax that has always been micro frontends' main cost. (blog.weskill.org 2026; Refonte Learning 2026)
- **Net assessment: stable architecture, better tooling, narrower recommended scope.** No new foundational principles in 2024-2026; the "complexity tax" critique remains the dominant caution, and the consensus continues to reserve micro frontends for genuine large-org scale. (10x.pub 2025; Feature-Sliced Design 2025)
