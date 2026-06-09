# Building AI Agent Platforms

**Domain:** 10 - AI, GenAI, and Agentic Systems
**Primary sources:** Building AI Agent Platforms (Ben O'Mahony and Fabian Nonnenmacher, 2026); with cross-references to AI Agent Systems (Bin Xu, 2026) and The MCP Standard (Sekar, 2026)

## Overview

An AI agent platform is the internal foundation that lets stream-aligned teams build, evaluate, deploy, and operate AI applications at scale without each team re-solving the same problems. The central insight of O'Mahony and Nonnenmacher is that AI applications are *mostly software*: AI engineering largely integrates existing foundation models rather than training bespoke ones, so its overlap with traditional software engineering is far greater than with core machine learning. Consequently a platform team should leverage the existing internal developer platform (IDP) rather than building a separate "special" runtime for AI, and add only the genuinely AI-specific capabilities on top.

What makes AI applications different is a small set of hard characteristics: they are non-deterministic and probabilistic (unreliable output, the one thing engineers normally avoid), they cannot be unit-tested the usual way and must be *evaluated* instead, they wrap a rapidly changing black-box LLM that cannot be treated as a drop-in replaceable component, and they have higher latency than typical API calls. A platform earns its keep by providing the tooling and services that tame these characteristics across the entire application lifecycle, while preserving team autonomy. This matters in the SDLC because, without a platform, every team independently rebuilds LLM access, RAG, orchestration, evaluation, guardrails, and observability, fragmenting effort and diffusing expertise that is already scarce in a young discipline.

## Core concepts

**Platform definition (Bottcher, via O'Mahony).** "A digital platform is a foundation of self-service APIs, tools, services, knowledge and support which are arranged as a compelling internal product. Autonomous delivery teams can make use of the platform to deliver product features at a higher pace, with reduced coordination." Three load-bearing aspects: it is more than infrastructure (it bundles capabilities with knowledge, support, and organizational patterns; in its simplest form just a well-documented set of sensible practices); it is **self-service** (provision, configure, manage resources with no central approval); and it is a **compelling internal product** that wins adoption through value, not mandate.

**AI applications vs microservices.** Similarities: both are independently deployable units modeling a business capability, of manageable size, containerizable when stateless, exposing and consuming defined APIs, and prone to error propagation (addressed with circuit breakers, retries, fallbacks). AI-specific characteristics: unreliable/probabilistic output, the management burden of a frequently-released black-box LLM (tuning to one model does not transfer; models differ in performance, cost, and API signatures), and higher latency. The practical takeaway: do not build a separate runtime for AI applications; they behave like services that call an LLM.

**The AI application lifecycle.** Broadly the same phases as traditional, ideally iterative software development: Discovery and Planning (define purpose, business value, cross-functional requirements, compliance/accuracy/safety targets early; the first MVP need not finalize everything), Design and Development (architecture, interfaces, decomposition; for agents, defining tools and RAG knowledge bases; choosing language, agent framework, and LLM provider/models), Testing (evaluation, not traditional tests), Deployment, and Operations and Monitoring.

**Guardrails.** Policies and automated checks that restrict an AI application's behavior to acceptable boundaries. They can be traditional software checks or additional LLM calls that verify output against defined guidelines. Combined with human-in-the-loop steps for high-stakes actions (confirm a travel plan before booking), they manage the reality that AI applications will never be perfect.

**Evaluation-Driven Development (EDD).** Define evaluation criteria *upfront*, before writing code, and use them to guide development so every change is assessed by its impact on metrics aligned to business goals. Requires an evaluation dataset of example inputs and expected responses, compared with defined metrics. EDD prevents aimless "coasting on vibes" development.

**Tracing and observability for AI.** Beyond logging, a dedicated tracing system records all internal interactions (input prompts, model responses, tool calls). Production inputs differ from the eval dataset, so tracing is essential for debugging, understanding real user behavior, and meeting compliance/audit needs.

## Frameworks & models

**Identifying core platform capabilities (the Pareto / 80-20 lens).** Optimize for global efficiency, not local optimization of individual teams. Focus on the ~20% of capabilities used by ~80% of teams. Finding that 20% requires deep empathy for stream-aligned teams (who often cannot articulate requirements, frequently due to the *XY Problem* of fixating on a preconceived solution), understanding common patterns across AI engineering to drive sensible defaults, and supporting a range of use cases (simple chatbot prototype to highly available multi-agent system) and expertise levels.

**Designing for extreme users.** Gather requirements not for the average user but for the extremes: the simplest and the most complex use cases, and the novice and expert users. Addressing the extremes covers the average in between and creates a growth path as use cases and expertise evolve.

**Three culinary metaphors for platform design.**
- **Mise-en-place ("everything in its place").** Capabilities must not just exist but be organized and composable so teams combine them without integration overhead: consistent auth/authorization, a unified observability backend, runtime composability (the agent runtime can reach the vector-DB service via injected connection details and pre-configured permissions). Not every dish needs every ingredient, but having them prepped makes building fast.
- **Omakase ("chef's choice").** For newcomers who do not yet know what "good" looks like, the platform team (the chefs) selects the best ingredients and combines them into a sensible default meal: the *golden path*. This is the rationale behind opinionated defaults (as in Ruby on Rails).
- **Walking skeletons / golden path.** Project templates and reference implementations are minimal, end-to-end implementations of common patterns covering the lifecycle (development, evaluation, deployment, monitoring), bundling default project structure, package managers, linting, testing, CI/CD, an agent framework, LLM-provider connection, and sensible prompt/eval/monitoring defaults. Keep them focused on common starting points; cover advanced capabilities (fine-tuning, multi-agent orchestration) in separate templates or guides.

**Core AI-specific capabilities a platform should provide.** Integrating LLM APIs, enabling RAG, providing workflow/agent orchestration tools, executing and tracking evaluations, operating and monitoring AI applications (logging plus tracing), guardrails, and (selectively) fine-tuning. Provide these as a catalogue teams pick from based on need, without restricting autonomy.

**Avoiding technology lock-in.** The ecosystem changes fast, so once teams depend on a tool it is hard to remove. Mitigations: open standards (OpenTelemetry for observability, MCP for agent tools), Bezos's *two-way-door* principle (prefer fast, reversible decisions; treat their failures as cheap learning), and careful abstraction. Beware the **Grim Wrapper** (Hohpe): an abstraction so thin or leaky it adds no value while coupling you to it.

**Shared-responsibility compliance model.** Like cloud providers and customers: the platform team ensures the *platform* is compliant and documents which requirements it covers; stream-aligned teams ensure *their applications* are compliant and use the platform compliantly (they hold the context, e.g. data classification). Independent processes and audits verify application compliance. Make compliance easy with compliant-by-default settings and templates rather than gatekeeping.

**Enablement and community.** A dedicated Enabling Team (Team Topologies) helps stream-aligned teams use the platform and upskill in AI engineering (training, workshops, pair programming) without owning platform delivery. Communities of Practice foster informal knowledge sharing. The pinnacle of success is *internal open source*: users contributing back to the platform.

## Patterns

**Leverage the existing IDP, add AI-specific capabilities on top** - Intent: reuse the organization's developer platform for the ~80% that is ordinary software and add only AI-specific services. When to use: any org with an existing IDP adopting AI engineering. Trade-offs: avoids reinventing runtimes and duplicating sensible defaults; requires the AI capabilities to integrate cleanly with existing tooling. *(O'Mahony)*

**Golden path / paved road templates (walking skeletons)** - Intent: ship opinionated, end-to-end project templates that get teams productive fast and embed macro-architecture and compliance rules. When to use: lowering the entry barrier for teams new to AI engineering. Trade-offs: accelerates onboarding and standardization and bakes in compliance; must stay focused and not balloon into a demo of every capability, and recommendations should be encouraged, not forced. *(O'Mahony)*

**Mise-en-place composability** - Intent: make platform components interoperable and composable at runtime via shared standards and automatic wiring. When to use: when a platform risks becoming a fragmented collection of guides and tools. Trade-offs: eliminates manual stitching; requires upfront investment in platform-wide standards (auth, observability, connection injection). *(O'Mahony)*

**Designing for extreme users** - Intent: gather requirements from the simplest and most complex use cases and from novices and experts. When to use: capability identification and prioritization. Trade-offs: covers the average and creates a growth path; risks over-engineering if the extremes are misjudged. *(O'Mahony)*

**Evaluation-Driven Development** - Intent: define eval criteria before coding and gate every change on metric impact. When to use: all AI application development. Trade-offs: prevents directionless development and provides a quality baseline; imperfect early evals must not become an excuse to delay release. *(O'Mahony)*

**Guardrails plus human-in-the-loop** - Intent: bound AI behavior with automated checks and insert human confirmation for high-stakes actions. When to use: any application where errors carry financial, safety, or compliance risk. Trade-offs: makes imperfect AI safe to deploy; adds latency, cost (extra LLM verification calls), and user friction. *(O'Mahony; reinforced by Sekar's HITL approval gate and Bin Xu's risk-aware control)*

**Shared-responsibility compliance** - Intent: split compliance accountability between platform (platform-level) and stream-aligned teams (application-level) with compliant-by-default settings. When to use: regulated enterprises adopting a self-service platform. Trade-offs: keeps self-service genuine while managing risk; requires clear documentation of what the platform covers and independent audit. *(O'Mahony)*

**Open-standards anti-lock-in** - Intent: adopt OpenTelemetry, MCP, and reversible (two-way-door) decisions to stay portable in a fast-moving ecosystem. When to use: every platform technology choice. Trade-offs: preserves flexibility; thin abstractions risk becoming Grim Wrappers that add coupling without value. *(O'Mahony)*

## Principles & best practices

- **Treat the platform as a product** with product-based funding, a product team, and continuous improvement from user feedback. Without a product team the platform stagnates into legacy.
- **Win adoption, do not mandate.** Needing a mandate usually signals an unmet user need; investigate the gap.
- **Self-service with no approvals.** Every approval step slows stream-aligned teams; genuine self-service means provisioning without going through a central team.
- **Reduce cognitive load.** The single most valuable thing a platform does. If it cannot, it is not a platform.
- **Validate business value early.** AI ROI is hard to predict upfront; release MVPs to small (possibly internal) audiences early to validate the central hypothesis with real feedback, with clear, measurable success criteria (e.g., time saved).
- **Accept imperfect accuracy and address it holistically**, involving business stakeholders (accept residual financial risk or add human review). Improve accuracy in order: prompt engineering, then RAG for domain/recent knowledge, then fine-tuning only when other strategies are insufficient (it demands significant expertise and resources).
- **Keep prompts in version control next to the calling code.** Newer models are robust to small prompt changes, prompts have limited reusability and are tied to a use case and model, so heavy prompt-management infrastructure is an anti-pattern (an MCP server for prompt hints is the reasonable exception).
- **Embrace change with modular design** so components (LLM providers) can be swapped, and grant team autonomy so teams react quickly without lengthy coordination.
- **Define, measure, and communicate SLAs** for platform services and manage the *noisy-neighbour* problem (rate limits, quotas, prioritization) since teams share services.
- **Invest in developer joy**, not just the absence of friction: fast feedback loops, modern tools, and moments of delight ("Individuals and interactions over processes and tools").

## Anti-patterns & pitfalls

Application-lifecycle anti-patterns:
- **AI for AI's sake.** Using prompts where `if` statements suffice. If deterministic software solves it, do that, it is better, safer, cheaper.
- **Reimplementing orchestration inside applications** (or worse, inside a non-deterministic agent). Use existing workflow/pipeline engines for deterministic ordered tasks.
- **The God agent.** A single agent meant to solve everything in a valuable, complex domain chooses correct actions poorly; prefer smaller specialized subsystems.
- **Building an agent for what should be a tool.** Often better to build one tool that does one thing well (and, if shared, expose it as an MCP server) and let higher-level agents call it.
- **Technology-driven (not problem-driven) development.** Integrating the latest craze (multi-agent, graph agents) without a use case; explore in small isolated experiments first.
- **Chatbot-only mindset.** Most future AI interactions will happen under the surface, deeply integrated into user journeys, not in a chat box.
- **Aimless development without evals**, and its opposite, **analysis paralysis** from chasing perfect evaluation before any release. Release early and safely (small user group or optional add-on) and harden evals from real feedback.

Platform anti-patterns:
- **The Golden Cage:** a golden path that allows no deviation, preventing teams from innovating.
- **Enforcing mandates instead of winning adoption.**
- **Self-service with approval** and **obscure self-service** (so complex or flaky that users treat the platform as a black box).
- **Backlog coupling:** stream-aligned teams blocked by the platform's roadmap.
- **A separate special runtime for AI applications:** large maintenance cost, new defaults to learn, often a full duplicate of the existing runtime.
- **Over-focusing on prompt management.**
- **Delegating complexity to users** (the opposite of reducing cognitive load).
- **Using a platform to hide organizational issues** (e.g., subverting weak data governance instead of fixing it).
- **Reimplementing existing tools and frameworks** rather than improving or adopting them.

## Decision guidance

- **Build a new capability vs reuse.** Identify the real problem first; reuse existing tools/frameworks and only reimplement when there is a major gap you cannot fill by improving the current offering. Apply the 80/20 lens: build what 80% of teams need.
- **Workflow engine vs graph agent vs raw agent (cross-ref multi-agent-systems.md).** Deterministic ordered flow goes to an external workflow engine; LLM-driven decisions inside a known structure go to a graph-based agent; reserve raw autonomy for open-ended tasks.
- **How to improve accuracy.** Prompt engineering first, RAG when domain or recent knowledge is needed, fine-tuning last. Pair all of it with evaluation so impact is measurable, and with guardrails plus human-in-the-loop where errors are costly.
- **When to mandate vs encourage.** Default to encouraging via golden paths and sensible defaults; reserve mandates for genuine cognitive-load reduction, never as a substitute for understanding user needs or as a compliance cudgel.
- **Compliance posture.** Do not gatekeep deployments; instead, document platform-covered requirements, ship compliant-by-default settings, and rely on the shared-responsibility model with independent audits, preserving real self-service.
- **Guarding against lock-in.** Prefer open standards (OpenTelemetry, MCP) and reversible decisions; abstract carefully to avoid Grim Wrappers.

## Source attribution

- **Entire chapter content** (platform definition, AI-vs-microservice comparison, lifecycle, AI-specific challenges and mitigation strategies, evaluation-driven development, tracing, application and platform anti-patterns, success factors, core-capability identification, designing for extremes, the three culinary metaphors, golden paths/templates, developer joy, cross-functional requirements, noisy neighbour, compliance shared-responsibility model, anti-lock-in strategies, enablement and community): Building AI Agent Platforms (O'Mahony and Nonnenmacher, 2026).
- **Risk-aware control, evaluation dimensions, and trace-first operation referenced in guardrails/observability/evaluation:** AI Agent Systems (Bin Xu, 2026).
- **MCP as the open standard for agent tools, the human-in-the-loop approval gate, and exposing shared capabilities as MCP servers:** The MCP Standard (Sekar, 2026).

## Recent developments (2024-2026)

- **Every major vendor shipped a managed agent platform, validating "buy the platform" alongside "build the IDP."** Microsoft Copilot Studio (plus Agent 365 control plane, Ignite 2025), Salesforce Agentforce, Google Vertex AI Agent Builder, AWS Bedrock AgentCore, and IBM watsonx Orchestrate now provide the golden-path, registry, observability, guardrail, and compliance capabilities this book argues a platform should offer, but as commercial products (vendor announcements, 2025-2026).
- **The build-vs-buy decision became quantifiable.** As of 2026, packaged platforms compete against custom stacks (Claude Agent SDK + Bedrock, OpenAI Agents SDK + Azure OpenAI, Vertex AI Agent Builder), with reported TCO crossing over around ~1M agent conversations/year, operationalizing this file's 80/20 and reuse-vs-build guidance (Digital Applied / industry analyses, 2026; figures vendor-sourced, treat cautiously).
- **The open-standards anti-lock-in principle was vindicated.** MCP became a Linux Foundation / Agentic AI Foundation standard (December 2025) and A2A a Linux Foundation project (June 2025); OpenTelemetry GenAI Semantic Conventions emerged as the observability standard. Building on MCP + OTel is now the concrete way to stay portable, exactly as this book recommends (Linux Foundation / OpenTelemetry, 2025-2026).
- **Evaluation-Driven Development gained tooling and a name shift toward "evals."** LangSmith, Langfuse, Arize, and Braintrust matured into eval-plus-tracing platforms; OpenTelemetry GenAI conventions standardized the trace data EDD depends on, turning "define evals before code" into a supported workflow rather than a discipline teams must hand-roll (Langfuse / OpenTelemetry, 2025-2026).
- **The "AI applications are mostly software" thesis held as agent SDKs converged.** OpenAI, Google, Anthropic, and a unified Microsoft Agent Framework (v1.0, April 2026) all converged on MCP, A2A, streaming, persistence, and observability, reinforcing that platform teams should layer AI-specific services onto an existing developer platform rather than build a separate runtime (Composio / Visual Studio Magazine, 2025-2026).
- **Context engineering joined the platform capability list.** Anthropic (September 2025) and LangChain (July 2025) formalized context engineering (write/select/compress/isolate, memory tools, just-in-time retrieval), an AI-specific capability platforms increasingly provide as defaults alongside RAG and orchestration (Anthropic / LangChain, 2025).
- **Governance moved from optional to regulated.** EU AI Act GPAI obligations took effect August 2025 (full high-risk applicability August 2026); NIST launched an AI Agent Standards Initiative (February 2026). The shared-responsibility compliance model this file describes now has external deadlines, making compliant-by-default golden paths more valuable (EU Commission / NIST, 2025-2026).

Sources: [Enterprise AI agent platforms (Sana)](https://sanalabs.com/agents-blog/leading-ai-enterprise-fortune-500), [Enterprise AI agent build vs buy 2026](https://www.digitalapplied.com/blog/enterprise-ai-agent-build-vs-buy-2026), [One Year of MCP](https://blog.modelcontextprotocol.io/posts/2025-11-25-first-mcp-anniversary/), [AI Agent Observability (OpenTelemetry)](https://opentelemetry.io/blog/2025/ai-agent-observability/), [EU AI Act timeline](https://artificialintelligenceact.eu/implementation-timeline/).
