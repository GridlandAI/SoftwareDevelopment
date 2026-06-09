# DevMentor

A suite of specialist advisors covering the **entire software development lifecycle beyond the coding itself**: from figuring out what to build, through specifying it, architecting it, choosing patterns, designing it well, scaling it, securing it, testing it, and documenting and delivering it.

DevMentor is grounded in a knowledge base distilled from **243 books and papers** on software development, enriched with current research through **2024-2026**. Each specialist reasons from its own bundled slice of that knowledge base.

## What is included

**1 orchestrator + 14 specialists (skills):**

| Skill | Helps with |
|-------|-----------|
| `devmentor-hub` | Entry point. Understands your need and routes to the right specialist; synthesizes across them. |
| `devmentor-requirements` | Requirements and specifications, user stories, BDD, acceptance criteria. |
| `devmentor-product-discovery` | Discovery, MVPs, impact mapping, lean analytics, estimation, roadmaps. |
| `devmentor-architect` | Architecture fundamentals, quality attributes, trade-offs, ADRs, style selection. |
| `devmentor-architecture-patterns` | Microservices, event-driven, serverless, SOA, integration, APIs, micro frontends. |
| `devmentor-software-design` | DDD, design patterns, clean architecture, coupling/cohesion, refactoring. |
| `devmentor-distributed-systems` | Data-intensive design, scalability, resilience, system design, event sourcing/CQRS. |
| `devmentor-data-architecture` | Data modeling, mesh, lakehouse, data engineering, knowledge graphs, semantic AI. |
| `devmentor-cloud-platform` | Cloud-native, containers, Kubernetes, IaC, SRE, MLOps, platform engineering. |
| `devmentor-ai-systems` | LLM/GenAI and agentic design, MCP, multi-agent, securing GenAI, emerging tech. |
| `devmentor-ai-assisted-dev` | AI coding assistants, vibe coding, keeping engineering discipline with AI. |
| `devmentor-enterprise-architecture` | EA frameworks, business architecture, modernization, agile enterprise. |
| `devmentor-quality-testing` | Test strategy, test design patterns, formal methods and verification. |
| `devmentor-ia-ux` | Information architecture, UX, design psychology, UX for AI, frontend architecture. |
| `devmentor-docs-delivery` | Documenting architecture, technical writing, Git/CI-CD delivery. |

**10 commands** (slash commands):

`/devmentor-spec`, `/devmentor-adr`, `/devmentor-architecture-review`, `/devmentor-recommend-patterns`, `/devmentor-tradeoff-analysis`, `/devmentor-system-design`, `/devmentor-tech-selection`, `/devmentor-test-strategy`, `/devmentor-modernization-plan`, `/devmentor-threat-model-genai`.

## How to use it

- Ask a broad question and let `devmentor-hub` route you, or
- Invoke a specialist directly by topic, or
- Run a command for a concrete deliverable, for example `/devmentor-adr should we adopt event sourcing for the orders service`.

## Installing

This folder is both a plugin and a single-plugin marketplace.

1. Add it as a marketplace (point at this `devmentor` folder).
2. Install the `devmentor` plugin from it.

Each specialist is also available as a standalone `.skill` file if you only want one.

## Knowledge base

Each skill carries its own `knowledge/` folder of synthesized topic files. Every topic file is structured as: Overview, Core concepts, Frameworks and models, Patterns, Principles and best practices, Anti-patterns and pitfalls, Decision guidance, Source attribution, and a "Recent developments (2024-2026)" section. The `devmentor-hub` skill also carries the cross-cutting pattern catalog, quality-attributes catalog, and source map.

## Conventions

American English, no em-dashes. The advisors aim to present trade-offs honestly, avoid hype, ground recommendations in the knowledge base, and produce concrete artifacts.
