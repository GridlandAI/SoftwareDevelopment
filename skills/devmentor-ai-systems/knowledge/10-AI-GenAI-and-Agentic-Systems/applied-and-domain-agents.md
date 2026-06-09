# Applied and Domain Agents

**Domain:** 10 - AI, GenAI, and Agentic Systems
**Primary sources:** *Insight Agents: An LLM-Based Multi-Agent System for Data Insights* (Bai, Zhang, Zhang, Zhu, Amazon, SIGIR 2025); *The Semantic Lifecycle in Embodied AI: Acquisition, Representation and Storage via Foundation Models* (Chen, Chen, Bei, Zhao, Zhou, Huang, IEEE, 2026); *Semantic Trading: Agentic AI for Clustering and Relationship Discovery in Prediction Markets* (Capponi, Gliozzo, Zhu, Columbia / IBM Research, 2025).

## Overview

This file captures how agentic AI is applied in three concrete domains in 2025-2026: production data-analysis assistants for e-commerce sellers, embodied AI that must acquire and maintain semantic knowledge of the physical world, and agentic discovery of latent structure in financial prediction markets. These are deployment-oriented (or near-deployment) systems, so the lessons are pragmatic: how to balance coverage, accuracy, and latency in a launched product; how to structure the flow of semantic knowledge through perception-to-action loops; and how to combine LLM semantic understanding with statistical validation to produce actionable, tradeable signals.

Across all three, a recurring architectural theme is **hierarchical decomposition with specialized roles** - a manager or planner directs specialized workers - combined with **grounding mechanisms** (APIs, retrieval, statistical validation, persistent memory) that keep the LLM honest. For software practitioners, these papers are reference designs for shipping agentic systems where reliability, latency, and verifiable outputs matter more than benchmark scores.

## Core concepts

**Plan-and-execute agentic paradigm (Bai et al.).** A query is first understood and planned, then executed through tool/function calls and data retrieval. Insight Agents (IA) builds an end-to-end agentic workflow on this paradigm, explicitly engineered for **comprehensive coverage, high accuracy, and low latency** simultaneously - the three competing constraints of a production assistant.

**Manager-worker hierarchy (Bai et al.).** A manager agent oversees subordinate worker agents. In IA: one manager agent plus two workers - a **data presenter agent** (descriptive analytics: "what were my sales last month") and an **insight generator agent** (diagnostic analysis: "how is my business doing vs. benchmarks"). Different query types route to different resolution paths.

**Out-of-domain (OOD) detection and routing (Bai et al.).** The manager first checks query eligibility against scope using a lightweight encoder-decoder OOD detector, then routes with a BERT-based classifier, and de-ambiguates via a query augmenter. Specialized lightweight models replace LLM calls where possible to cut latency; LLMs are reserved for data fusion and generation.

**Semantic information in embodied AI (Chen et al.).** Structured knowledge bridging raw sensory perception and purposeful action - not just object labels but spatial relations, attributes, affordances (graspability), and procedural hierarchies essential for planning and control. Embodied semantics must be **action-relevant and environment-grounded**, unlike traditional visual recognition.

**The Semantic Lifecycle (Chen et al.).** A unified framework characterizing how semantic knowledge evolves within embodied AI driven by foundation models, replacing the view of semantic processing as isolated modules. Three coupled stages: **acquisition** (scene-centric or agent-centric perception), **representation** (alignment across modalities and reference frames), and **storage** (persistent, incrementally updated as the agent accumulates experience).

**Multi-source, multi-stage semantics (Chen et al.).** Embodied agents must integrate heterogeneous sources (vision, language, proprioception, interaction traces) and reconcile egocentric observations with allocentric world models. Each lifecycle stage imposes distinct computational and architectural requirements yet remains deeply coupled - the core difficulty.

**Prediction-market fragmentation (Capponi et al.).** Prediction markets aggregate dispersed information into price signals but fragment: overlapping near-duplicate questions, implicit equivalences, and hidden contradictions across contracts. This raises search costs, fragments liquidity, and obscures hedging/mispricing opportunities.

**Semantic structure plus statistical validation (Capponi et al.).** The key design choice: combine LLM semantic understanding (to narrow the candidate space to pairs that share meaning) with outcome-based statistical dependence analysis on resolved markets (to keep only empirically reliable relationships). Neither embedding similarity alone nor correlation-only screening suffices.

## Frameworks & models

**Insight Agents (IA) production architecture (Bai et al.).** Launched for Amazon sellers in the US; ~90% accuracy on human evaluation with P90 latency under 15 seconds.
- *Manager agent:* OOD detection (encoder-decoder), agent routing (BERT classifier), query augmenter for de-ambiguation, plus a guardrail post-processing step (blocks PII leakage, toxic content).
- *Data presenter agent:* strategic planning for an API-based data model, decomposing queries into granular components matched to internal data-API availability (divide-and-conquer), with automated tabular retrieval and aggregation via task decomposition, planning, and API/function selection.
- *Insight generator agent:* dynamically injects domain knowledge based on the query to make insights domain-aware.
- *Foundation:* built on Retrieval-Augmented Generation (RAG) over tabular seller data.
- *Latency engineering:* specialized ML models avoid unnecessary LLM calls; OOD detection and routing run in parallel; both worker branches initiate concurrently with early termination (trading infrastructure cost for latency).

**The Semantic Lifecycle framework (Chen et al.).** A holistic perspective on the continuous flow and maintenance of semantic knowledge in foundation-model-driven embodied AI, analyzed across acquisition, representation, and storage:
- *Acquisition:* foundation models supply cross-domain generalization and rich semantic priors, replacing task-specific perception pipelines; scene-centric and agent-centric perception gather action-relevant semantics.
- *Representation:* alignment of semantics across modalities (vision, language, proprioception) and across egocentric/allocentric reference frames.
- *Storage:* persistent semantic maps with incremental updating as the agent accumulates experience, sustaining stable perception-to-action loops.

**Semantic Trading agentic pipeline (Capponi et al.).** An end-to-end pipeline on Polymarket data:
1. *Topic clustering:* an LLM-based agent reads contract text and metadata to cluster markets into coherent topical groups, with interpretable cluster labeling.
2. *Within-cluster relationship discovery:* identifies market pairs with strong outcome dependence - "same-outcome" (correlated) and "different-outcome" (anti-correlated) links.
3. *Statistical validation:* outcome-based dependence analysis on resolved markets filters to empirically reliable relationships.
4. *Trading strategy synthesis:* operationalizes discovered links into a transparent baseline pair strategy over pre-resolution price paths, measured by both classification metrics (precision/recall) and economic metrics (PnL, Sharpe).
- *Results:* agent-identified relationships ~60-70% accurate; induced strategies ~20% average return over week-long horizons. Positions agentic AI as a scalable "discovery layer" connecting fragmented contracts into a structured, queryable, tradeable graph of propositions.

## Patterns

**Manager-worker routing with OOD gating** - Intent: serve heterogeneous query types reliably while controlling scope and latency. When to use: production conversational/analytics assistants with bounded domains. A manager handles OOD detection, routing, and de-ambiguation; specialized workers handle distinct query classes. Trade-offs: requires training specialized routers/detectors; misrouting degrades quality; adds an orchestration layer.

**Model-based components over LLM calls** - Intent: cut latency and cost in production agents. When to use: latency-sensitive deployments where some sub-tasks (detection, classification) do not need full LLM reasoning. Replace LLM calls with lightweight specialized models; reserve the LLM for fusion and generation. Trade-offs: more models to train and maintain; specialized models may be less flexible than an LLM.

**Parallel branches with early termination** - Intent: reduce end-to-end latency. When to use: when multiple resolution paths might apply and you can afford redundant compute. Initiate candidate branches concurrently and terminate early once a path resolves. Trade-offs: increased infrastructure and compute cost for latency reduction.

**Query decomposition to data-API grain** - Intent: produce accurate answers over structured/tabular data. When to use: agents answering analytical questions backed by APIs. Break the query into granular components matched to available data-API endpoints (divide-and-conquer), then retrieve and aggregate. Trade-offs: depends on API coverage; decomposition errors propagate.

**Dynamic domain-knowledge injection** - Intent: make generated insights domain-aware without retraining. When to use: insight/explanation generation across varied business contexts. Inject relevant domain knowledge into the generator conditioned on the query. Trade-offs: requires a curated, query-addressable knowledge source.

**Lifecycle-staged semantic memory** - Intent: maintain stable, evolving world knowledge in embodied agents. When to use: robots/embodied agents operating in open-ended environments over time. Treat semantics as a lifecycle - acquire (perception), represent (cross-modal/cross-frame alignment), store (persistent, incrementally updated maps). Trade-offs: each stage has distinct compute/architecture needs and the stages are tightly coupled, making the system complex.

**Semantic-narrow-then-statistically-validate** - Intent: surface reliable relationships from large, noisy candidate spaces. When to use: discovery tasks combining meaning and empirical evidence (linked markets, related entities). Use LLM semantics to cluster and propose candidates, then validate with statistical dependence analysis on ground-truth outcomes. Trade-offs: requires resolved/ground-truth data for validation; LLM proposals are imperfect (~60-70% accuracy here).

**Guardrail post-processing** - Intent: prevent unsafe or non-compliant outputs reaching users. When to use: any user-facing production agent. Apply a guardrail step (PII detection, toxicity filtering) before returning responses. Trade-offs: adds latency; guardrails can over-block legitimate content.

## Principles & best practices

- **Engineer for the coverage-accuracy-latency triangle explicitly.** Production agents must satisfy all three at once, not optimize one (Bai et al.).
- **Reserve the LLM for what only it can do** (fusion, generation); push everything else to cheaper specialized models (Bai et al.).
- **Decompose queries to the grain of your data APIs** for accuracy on structured data (Bai et al.).
- **Always guardrail user-facing output** for PII and safety (Bai et al.).
- **Treat embodied semantics as action-relevant and grounded**, not as static labels - affordances and spatial/procedural relations matter for control (Chen et al.).
- **Model semantic knowledge as a continuous lifecycle**, not as disjoint perception/representation/storage modules (Chen et al.).
- **Combine semantic understanding with statistical validation** for trustworthy discovery; neither alone is sufficient (Capponi et al.).
- **Measure both predictive and economic/task outcomes**, not just classification accuracy, for applied agents (Capponi et al.).

## Anti-patterns & pitfalls

- **Routing every sub-task through the LLM.** Inflates latency and cost; defeats production viability (Bai et al.).
- **No OOD/scope gating.** Lets out-of-scope queries produce confidently wrong answers (Bai et al.).
- **Shipping user-facing agents without guardrails.** Risks PII leakage and toxic output (Bai et al.).
- **Treating embodied semantics as object recognition.** Misses affordances, spatial relations, and procedural hierarchies needed for action (Chen et al.).
- **Isolated, task-specific semantic pipelines.** Cannot sustain stable perception-to-action loops across open-ended tasks; do not generalize (Chen et al.).
- **Relying on embedding similarity or correlation alone for relationship discovery.** Embedding similarity misses logical/economic links; correlation-only screening needs long histories and is confounded by sparse, one-time-resolving contracts (Capponi et al.).
- **Reporting only classification metrics for trading agents.** Hides whether discovered relationships are actually profitable or stable (Capponi et al.).

## Decision guidance

For applied/domain agentic systems:

1. **Identify the hard constraints.** Production assistant -> coverage, accuracy, and latency are co-equal; design a manager-worker hierarchy with OOD gating and parallel early-terminating branches.
2. **Decide where the LLM is necessary.** Use specialized lightweight models for detection/routing/classification; reserve LLMs for fusion and generation.
3. **Ground the agent.** Structured-data tasks -> decompose to data-API grain plus RAG; discovery tasks -> validate semantic candidates statistically against ground truth; embodied tasks -> maintain a lifecycle-staged semantic memory.
4. **Add safety at the boundary.** Guardrail all user-facing outputs.
5. **Choose evaluation metrics that reflect real value.** Human evaluation and latency percentiles for assistants; precision/recall plus PnL/Sharpe for trading; perception-to-action stability for embodied agents.

## Source attribution

- Plan-and-execute paradigm, manager-worker hierarchy, OOD detection and routing, data presenter and insight generator agents, latency engineering (specialized models, parallel early-terminating branches), query decomposition to data-API grain, dynamic domain-knowledge injection, RAG foundation, guardrails, production results: Bai, Zhang, Zhang, Zhu, *Insight Agents: An LLM-Based Multi-Agent System for Data Insights* (Amazon, SIGIR 2025).
- Embodied semantic information, action-relevant grounding, the Semantic Lifecycle (acquisition/representation/storage), multi-source/multi-stage semantics, egocentric/allocentric reconciliation, foundation-model role across stages, perception-to-action loops: Chen, Chen, Bei, Zhao, Zhou, Huang, *The Semantic Lifecycle in Embodied AI* (IEEE, 2026).
- Prediction-market fragmentation, semantic-clustering plus statistical-validation pipeline, same-outcome/different-outcome relationship discovery, trading-strategy synthesis, predictive and economic evaluation, discovery-layer framing: Capponi, Gliozzo, Zhu, *Semantic Trading: Agentic AI for Clustering and Relationship Discovery in Prediction Markets* (Columbia University / IBM Research, 2025).

## Recent developments (2024-2026)

- **Agentic assistants are reaching production at scale.** Insight Agents is a launched Amazon product hitting ~90% accuracy at sub-15s P90 latency, demonstrating that the manager-worker + specialized-model + RAG pattern is production-viable, not just a research design (Bai et al., 2025).
- **Latency-first agent engineering** (replacing LLM calls with lightweight models, parallelizing branches with early termination, guardrailing outputs) is emerging as standard practice for shipped agentic products (Bai et al., 2025).
- **Foundation models are reshaping embodied AI semantics**, replacing task-specific perception pipelines with generalizable, semantically rich capabilities spanning a unified acquisition-representation-storage lifecycle (Chen et al., 2026).
- **Agentic AI as a discovery layer for financial markets** is a 2025 development: LLM agents combined with statistical validation uncover latent semantic structure (correlated/anti-correlated contracts) in fragmented prediction markets, translating into measurable trading edge (~20% week-horizon returns) (Capponi et al., 2025).
- **The shared architectural pattern across domains** - hierarchical specialization plus a grounding/validation mechanism (APIs, RAG, statistical validation, persistent semantic memory) - is consolidating as the reliable way to deploy LLM agents where correctness and latency matter.

Broader 2025-2026 context for deploying domain agents:

- **Enterprise agent platforms commoditized the manager-worker plus grounding pattern.** Salesforce Agentforce (reported 22,000+ deals in a single quarter), Microsoft Copilot Studio with the Agent 365 governance control plane (Ignite 2025), Google Vertex AI Agent Builder, IBM watsonx Orchestrate, and AWS Bedrock AgentCore now ship the routing, RAG, guardrail, and monitoring scaffolding these papers hand-built, as managed services (vendor announcements, 2025-2026; figures vendor-sourced).
- **MCP and A2A standardized the tool and inter-agent layer.** MCP became a Linux Foundation standard (December 2025) with an official registry; A2A moved to the Linux Foundation (June 2025). Domain agents increasingly reach data APIs and peer agents over these standards rather than bespoke integrations (Linux Foundation, 2025).
- **Latency-first engineering and guardrails became mainstream practice.** The Insight Agents recipe (replace LLM calls with lightweight specialized models, parallelize branches with early termination, guardrail all outputs) is now standard advice for shipped agentic products, and observability tooling (OpenTelemetry GenAI conventions, LangSmith, Langfuse, Datadog AI Agent Monitoring) makes the coverage-accuracy-latency triangle measurable in production (OpenTelemetry / ecosystem, 2025-2026).
- **Agent benchmarking matured with caveats relevant to applied claims.** SWE-bench Verified, Terminal-Bench, OSWorld, and GAIA became standard, but 2026 analyses of contamination and reward hacking reinforce these papers' emphasis on real-world metrics (human eval, latency percentiles, PnL/Sharpe, perception-to-action stability) over headline benchmark scores (benchmarkingagents.com, 2026).

Sources: [Enterprise AI agent platforms (Sana)](https://sanalabs.com/agents-blog/leading-ai-enterprise-fortune-500), [One Year of MCP](https://blog.modelcontextprotocol.io/posts/2025-11-25-first-mcp-anniversary/), [AI Agent Observability (OpenTelemetry)](https://opentelemetry.io/blog/2025/ai-agent-observability/), [Agent benchmarks 2026](https://benchmarkingagents.com/agent-benchmarks/).
