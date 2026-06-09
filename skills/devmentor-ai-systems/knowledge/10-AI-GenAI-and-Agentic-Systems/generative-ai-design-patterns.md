# Generative AI Design Patterns

**Domain:** 10 - AI, GenAI, and Agentic Systems
**Primary sources:** *Generative AI Design Patterns: Solutions to Common Problems in Building GenAI Applications* (Lakshmanan & Hapke, 2025); *Mastering Generative AI: Design Patterns, Frameworks* (Singh, 2025)

## Overview
GenAI is easy to prototype and hard to productionize, because foundational models hallucinate, are nondeterministic, and have surprising gaps from how they were trained. *Generative AI Design Patterns* (Lakshmanan & Hapke) catalogs 32 proven, named patterns - each with a problem, a solution, a worked example, and considerations/alternatives - grouped by the recurring problem they solve: controlling content style, adding enterprise knowledge (RAG), extending model capabilities (reasoning and tuning), improving reliability, enabling agents to act, addressing cost/latency constraints, and setting safeguards. The book frames this practice as "AI engineering": building on top of foundational models (GPT, Gemini, Claude, Llama, DeepSeek, Qwen, Mistral) rather than training models from scratch, and progressively making applications more *agentic* by composing simple patterns.

*Mastering Generative AI* (Singh) complements this with a systems-and-frameworks view: a 7-step framework for designing GenAI systems, the GenAI application stack and architecture layers, multimodal evaluation (text, image, audio, video), inference optimization, model-selection strategy, and a set of nine higher-level architectural design patterns. Together the two books give both the fine-grained pattern catalog and the surrounding application stack.

## Core concepts
- **Foundational/frontier models.** Application-agnostic pretrained models. Frontier (GPT-5, Gemini 2.5 Pro, Claude) for hardest reasoning; distilled models (Gemini Flash, Claude Sonnet, GPT-4o-mini) for cost/speed; open-weight (Llama, Mistral, DeepSeek, Qwen) for control/customization; locally hostable (Llama 8B, Gemma 2B) for privacy/air-gapped use.
- **Prompt, context, system vs. user prompt.** The instruction plus role/context; system prompts set durable behavior, user prompts carry the dynamic task. Both can be multimodal.
- **Fine-grained generation control.** Logits, temperature, top-k, nucleus (top-p) sampling, and beam search trade determinism for diversity.
- **In-context learning.** Zero-shot, few-shot. Post-training methods: supervised fine-tuning, preference tuning/RLHF, adapter/LoRA/QLoRA.
- **Grounding.** Making the model answer from trusted sources (RAG) rather than parametric memory.
- **Agents and agentic applications.** Software components that use a model to plan, pick tools, recover from errors, and judge completion. Copilots are assistive; agents are autonomous; both are "agentic." Andrew Ng's four agentic patterns (reflection, tool use, planning, multi-agent) recur throughout.
- **Multimodality.** Text, image, audio, and video generation and understanding, each with its own evaluation metrics.

## Frameworks & models
- **The four problem families (Lakshmanan & Hapke).** (1) Control content/style; (2) add knowledge via RAG; (3) extend capabilities via reasoning and tuning; (4) constrain cost/latency and set safeguards. Pick the chapter that matches your problem, then the pattern within it.
- **Index-aware retrieval (RAG building block).** A bundle of techniques - hypothetical answers, query expansion, hybrid search (keyword + semantic), and GraphRAG - layered onto semantic indexing to fix the chicken-and-egg mismatch between user questions and indexed text.
- **The GenAI application stack (Singh).** UI layer (streaming responses, client caching, multimodal input) -> API gateway/orchestration (routing, auth, rate limiting, workflow orchestration, enterprise integration) -> model layer (serving, model registry, prompt management, caching, model routing/ensembles) -> knowledge/data layer (vector stores, retrieval).
- **7-step GenAI system design (Singh).** Define objective (measurable business KPIs) -> data strategy (collection, anonymization/PII via NER, expert annotation, vector indexing, versioning/lineage) -> model selection and training (transfer learning, LoRA/QLoRA, prompt/CoT, evaluation metrics) -> system architecture (microservices + agent orchestration via LangChain/CrewAI, Kubernetes, queues, vector DB) -> deployment -> integration/fine-tuning -> evaluation/monitoring.
- **Model selection matrix (Singh).** Weight criteria (task performance, domain alignment, reasoning, context window, cost/1K tokens, latency, fine-tuning/RAG support, hallucination control, compliance, on-premise) and score candidates (e.g., GPT-4 vs. Claude 3 vs. Mistral) with weighted sums.
- **Multimodal evaluation (Singh).** Text: BLEU, ROUGE, perplexity, human eval. Image: FID, Inception Score, SSIM, LPIPS, CLIP score. Audio: PESQ and others. Video metrics. Plus fairness measures (demographic parity, equality of opportunity, equalized odds, counterfactual fairness).
- **Composable agentic workflow (Lakshmanan & Hapke, Ch. 10).** Per Anthropic's "build effective agents" guidance, prefer simple, composable patterns over heavy frameworks; the book's capstone composes patterns (task-assigner, specialized writers, two-stage review) into a copilot/agent that improves via logged human feedback.

## Patterns
The 32 named patterns (Lakshmanan & Hapke), grouped by problem:

**Controlling content/style**
- **Logits Masking** - Intent: enforce style/format rules by intercepting the sampling stage and zeroing disallowed tokens. When to use: rules where continuations can be censored leaving valid options (branding, compliance). Trade-offs: needs backtracking when censoring removes all options.
- **Grammar** - Intent: constrain output to a metasyntax (BNF or a Pydantic model) so generated text is always valid (JSON, SQL). When to use: downstream consumers need parseable structure. Trade-offs: more reliable than "ask for JSON" but requires defining the grammar/schema.
- **Style Transfer** - Intent: use input-output example pairs to mimic a target tone/style. When to use: content exists but in the wrong style; conformance is not strict. Trade-offs: simple but only implicit enforcement; fine-tuning raises success rate at higher complexity.
- **Reverse Neutralization** - Intent: generate via an intermediate neutral form, then fine-tune a model to restyle neutral -> personal style. When to use: you want personal style but have no example in the target topic. Trade-offs: depends on a repeatable, unbiased neutral form.
- **Content Optimization** - Intent: use preference tuning to optimize style for an outcome, reframing A/B testing's weaknesses away. When to use: optimizing content toward a measurable goal. Trade-offs: requires an evaluator and preference data.

**Adding knowledge (RAG)**
- **Basic RAG** - Intent: ground responses in retrieved trusted knowledge. When to use: static knowledge cutoff or enterprise data the model never saw. Trade-offs: style patterns cannot add knowledge; quality bounded by retrieval.
- **Semantic Indexing** - Intent: embed chunks into a vector store and retrieve by semantic similarity (incl. images/tables). When to use: keyword indexing fails on complex/multimodal/cross-domain docs. Trade-offs: needs chunking and embedding-model choices; domain-specific models can help.
- **Indexing at Scale** - Intent: use metadata for disambiguation, freshness, and conflict handling as the knowledge base grows. When to use: production RAG that degrades over time. Trade-offs: effectiveness depends entirely on metadata quality/consistency.
- **Index-Aware Retrieval** - Intent: add hypothetical answers, query expansion, hybrid search, and GraphRAG to bridge question-document mismatch. When to use: questions absent from the KB, jargon mismatch, fine details, multi-chunk answers. Trade-offs: expansions can introduce hallucinated/obsolete/irrelevant matches and shift user intent.
- **Node Postprocessing** - Intent: combine multiple retrievers and rerank/filter chunks between retrieval and generation. When to use: similarity is not relevance; need disambiguation, freshness, personalization. Trade-offs: rerankers add latency/cost (mitigate with a small model like BGE) but only if you do not fold tasks into one LLM call.
- **Trustworthy Generation** - Intent: build user trust via out-of-domain detection, citations, human-in-the-loop, validation, and UI design (incl. self-RAG). When to use: production apps where wrong answers erode trust. Trade-offs: self-RAG adds pipeline complexity and failure points; threshold tuning is domain-specific.
- **Deep Search** - Intent: iterative retrieve-think-generate loop using tools (search, enterprise APIs) until "good enough" or budget exhausted. When to use: multi-hop, verification-heavy, deep-reasoning queries beyond a single retrieval. Trade-offs: slow and costly; parallelize subqueries and cut iterations short.

**Extending model capabilities**
- **Chain of Thought** - Intent: prompt for step-by-step reasoning (zero-shot, few-shot, auto-CoT). When to use: math, logic, sequential reasoning; domain-specific procedures. Trade-offs: no help when the failure is a data gap or the logic is non-sequential.
- **Tree of Thoughts (ToT)** - Intent: treat problem-solving as a tree search with explicit evaluation, backtracking, and restart. When to use: strategic/creative problems needing multiple directions. Trade-offs: significant overhead; start with CoT and escalate only if needed.
- **Adapter Tuning** - Intent: parameter-efficient fine-tuning via small adapter layers, cheap to train and swap at inference. When to use: specialize a base model for a task without full fine-tuning. Trade-offs: variations include merged models and provider-hosted fine-tunes.
- **Evol-Instruct** - Intent: post-train on a synthetically evolved instruction dataset (evolve instructions, generate answers, filter, instruction-tune) for enterprise tasks with no consumer analog. When to use: complex enterprise tasks the base model has not seen. Trade-offs: dataset size scales with task complexity / inverse model size (~10k examples per 1B params for moderate tasks); risk of forgetting other tasks (use only within the trained scope).

**Improving reliability**
- **LLM-as-Judge** - Intent: use an advanced model to evaluate outputs (prompting, ML, or fine-tuned), scaling beyond human eval. When to use: open-ended outputs that traditional metrics cannot score. Trade-offs: nondeterminism and biases; reduce via temperature 0, caching, fixed seeds, but similar outputs should still score similarly.
- **Reflection** - Intent: invoke the model twice or more - generate, critique via an evaluator (LLM/tool/human), revise - until a quality bar is met. When to use: stateless API calls that need self-correction. Trade-offs: extra calls; one of Ng's four core agentic patterns.
- **Dependency Injection** - Intent: replace LLM calls and external tools with mocks during development/testing of other components. When to use: testing nondeterministic GenAI code. Trade-offs: hardcoded mock values get harder to maintain as inter-step interaction grows.
- **Prompt Optimization** - Intent: add a level of indirection (e.g., DSPy) so prompts auto-update when models or dependencies change. When to use: brittle apps that break on model-version changes. Trade-offs: more robust than manual prompt management/versioning, which still requires manual re-experimentation.

**Enabling agents to act**
- **Tool Calling** - Intent: let the model invoke external functions/APIs (OpenAI Responses API, LangGraph, MCP) to act beyond generating text. When to use: tasks needing calculations, bookings, refunds, database writes. Trade-offs: reliability depends on clear function names/descriptions/enums and system-prompt policies; increases adversarial-attack surface.
- **Code Execution** - Intent: have the model write and run code to compute results. When to use: precise computation/transformation the model cannot do reliably in text. Trade-offs: sandboxing and security required.
- **Multiagent Collaboration** - Intent: divide cognitive labor across specialized agents (hierarchical, peer-to-peer, market-based, hybrid) to beat single-agent limits. When to use: multistep tasks spanning domains, parallelizable work, need for verification and horizontal scaling. Trade-offs: orchestration complexity; coordinate human intervention points; can show emergent behavior (and emergent failure).

**Addressing constraints**
- **Small Language Model** - Intent: use a smaller/distilled model for appropriate subtasks. When to use: cost/latency-sensitive, high-volume, or on-device tasks. Trade-offs: reduced capability; route only suitable tasks to it.
- **Prompt Caching** - Intent: reuse prior responses (semantic/response cache) or model state (prefix/context cache), client- or server-side. When to use: repetitive queries (outage reports, login issues, store hours). Trade-offs: client-side cuts latency most but you operate the cache and it is per-client; server-side suits distributed clients/managed providers.
- **Inference Optimization** - Intent: reduce inference cost/latency (batching, quantization, efficient serving). When to use: production scale under hardware/cost pressure. Trade-offs: precision/accuracy vs. efficiency.
- **Degradation Testing** - Intent: test how the system behaves as quality/latency degrade. When to use: guarding production SLAs. Trade-offs: extra test infrastructure.
- **Long-Term Memory** - Intent: persist user history, task context, and feedback across sessions. When to use: personalization and context continuity. Trade-offs: storage, retrieval, and privacy management.

**Setting safeguards**
- **Template Generation** - Intent: generate within fixed templates to constrain structure. When to use: regulated/branded outputs. Trade-offs: less flexibility.
- **Assembled Reformat** - Intent: assemble validated pieces then reformat. When to use: combining trusted fragments safely. Trade-offs: more pipeline steps.
- **Self-Check** - Intent: have the model verify its own output against criteria before returning. When to use: catch errors pre-delivery. Trade-offs: extra call; not a substitute for external validation.
- **Guardrails** - Intent: layers operating on inputs, outputs, context, and tool parameters to enforce security, privacy, content moderation, anti-hallucination, and alignment (prebuilt: NeMo, Guardrails AI, LLM Guard; provider safety settings). When to use: any production app, especially public-facing or tool-calling. Trade-offs: centralize checks to avoid sprinkling error-handling across the codebase; guards add latency.

**Higher-level architectural patterns (Singh)**
- **Layered Caching -> Fine-Tuning** - Intent: progressive caching (prompt/embedding/response) that feeds collected data into targeted fine-tuning. When to use: high-volume domains (support, personalization). Trade-offs: monitor added-service latency (GPTCache/Redis).
- **Multiplexing AI Agents (panel of experts)** - Intent: orchestrator decomposes a query and routes subtasks to specialized agents, then synthesizes. When to use: multidisciplinary problems. Trade-offs: coordination overhead; orchestrator must preserve holistic view.
- **Multi-task fine-tuning** - Intent: shared backbone with task-specific heads trained jointly. When to use: one model serving summarization/QA/classification. Trade-offs: cross-task interference vs. knowledge transfer.
- **Blending rule-based + generative** - Intent: deterministic rules for high-risk/compliance plus LLM flexibility, with validation against the ruleset. When to use: regulated domains (IVR, healthcare, finance, legal). Trade-offs: dual maintenance of rules and prompts.
- **Knowledge graphs + LLMs** - Intent: combine structured factual graphs with LLM reasoning for grounded, trustworthy output. When to use: enterprises needing traceability and factual grounding. Trade-offs: graph construction/maintenance.
- **Swarm of agents** - Intent: multiple agents collaborate/compete (voting/consensus) for reliability/creativity. When to use: hard problems benefiting from diversity. Trade-offs: cost and coordination.
- **Modular monolith LLM architecture** - Intent: composable modular capabilities with standardized interfaces and reusable prompt components. When to use: maintainable single-deployable GenAI systems. Trade-offs: boundary governance.
- **Memory cognition layer** - Intent: a memory layer tracking history, task context, and feedback for personalization/continuity. When to use: cross-session assistants. Trade-offs: storage and privacy.
- **Red & blue team dual-model evaluation** - Intent: one model generates, another critiques/defends, to stress-test robustness and trust. When to use: safety/robustness evaluation. Trade-offs: extra compute.

## Principles & best practices
- Prefer simple, composable patterns over heavy frameworks (Anthropic via Lakshmanan & Hapke); compose patterns into agentic workflows.
- Be model/framework/cloud agnostic; use LLM-agnostic frameworks (PydanticAI, LangChain) so you can swap providers and balance quality/cost/speed with best/default/small model tiers.
- Match the pattern to the problem family: style -> Chapter 2 patterns; knowledge -> RAG family; capability/reasoning -> Chapter 5; reliability -> Chapter 6; action -> Chapter 7; constraints -> Chapter 8; safety -> Chapter 9.
- Engineer the context (system prompt, examples, retrieved knowledge), not just the instruction.
- Collect human feedback implicitly through the UI (AI recommendation + editable artifact) to keep improving the system.
- Use Grammar/structured output to make downstream consumers robust to nondeterminism.
- Centralize safeguards in guardrail layers rather than scattering checks.

## Anti-patterns & pitfalls
- Begging for compliance in the prompt ("please output valid JSON") and hoping; relying on instruction-following is brittle, costly, and forces every consumer to guard against failure. Use Grammar instead.
- Treating retrieval similarity as relevance; add Node Postprocessing/reranking.
- Letting query expansion silently change user intent or pull in hallucinated/obsolete matches.
- Unbounded Deep Search / multi-agent loops without cost/time budgets.
- Scaling model size when a smaller model + caching/routing would suffice.
- Using a heavyweight multi-agent framework when composable single-agent patterns would do.
- Fine-tuning when the gap is knowledge (use RAG) or simple instruction (use prompting).

## Decision guidance
- **Style enforcement strength.** Strict/parseable -> Grammar or Logits Masking; soft style -> Style Transfer or fine-tuning; outcome-optimized -> Content Optimization.
- **Knowledge.** Single doc -> stuff context; enterprise corpus -> Semantic Indexing + Indexing at Scale; question-doc mismatch -> Index-Aware Retrieval; relevance/freshness -> Node Postprocessing; deep/multi-hop -> Deep Search.
- **Reasoning.** Sequential -> CoT; exploratory -> ToT; self-correction -> Reflection; consistent evaluation -> LLM-as-Judge.
- **Customization.** Light task adaptation -> Adapter Tuning; novel enterprise task -> Evol-Instruct; prompt brittleness -> Prompt Optimization.
- **Action.** External effects -> Tool Calling (MCP); computation -> Code Execution; complex division of labor -> Multiagent Collaboration.
- **Cost/latency.** Repetitive traffic -> Prompt Caching; suitable subtasks -> Small Language Model; serving pressure -> Inference Optimization.
- **Safety.** All production -> Guardrails + Self-Check; regulated -> Template Generation / blending rule-based + generative.
- **Model selection (Singh).** Score candidates on weighted criteria (performance, cost, latency, context window, compliance, hallucination control, on-premise) rather than defaulting to the largest model.

## Source attribution
- The 32 named patterns, the four problem families, AI-engineering framing, foundational-model landscape, agentic characteristics, and composable agentic workflow: *Generative AI Design Patterns* (Lakshmanan & Hapke, 2025), Chapters 1-10.
- The GenAI application stack/architecture layers, 7-step system-design framework, model-selection matrix, multimodal evaluation metrics, inference optimization, and the nine higher-level architectural patterns: *Mastering Generative AI* (Singh, 2025).
- Cross-reference: prompting/RAG/agentic patterns at the LLM-internals level in `llm-design-patterns.md`; enterprise adoption, governance, and context engineering in `llms-in-enterprise-and-context-engineering.md`.

## Recent developments (2024-2026)

- **The frontier-model roster this file names shifted forward.** GPT-5 (August 2025) unified the reasoning and generalist tracks behind a thinking-budget parameter; Gemini 3 Pro (November 2025) and Claude Opus 4.5 (November 2025) are the new top tier; open-weight DeepSeek-V3.2 (December 2025, MoE + sparse attention) rivals GPT-5 on reasoning. The model-selection-matrix pattern is more relevant than ever as benchmark gaps narrow while price gaps widen toward ~2 orders of magnitude (Vellum / DeepSeek, 2025-2026).
- **Tool Calling standardized on MCP, now a Linux Foundation standard.** MCP moved to the Agentic AI Foundation (December 2025) with an official registry (~2,000 servers); the Tool Calling pattern's "OpenAI Responses API / LangGraph / MCP" options have consolidated around MCP for portable, discoverable tools (modelcontextprotocol.io, 2025).
- **RAG patterns evolved into agentic and graph-aware retrieval.** "Agentic RAG" (planned, reflective, multi-step retrieval) and Microsoft GraphRAG became mainstream, extending the Index-Aware Retrieval, Deep Search, and knowledge-graph patterns here with agent orchestration over the retrieval pipeline (IBM / survey literature, 2024-2025).
- **Multiagent Collaboration gained quantitative guardrails.** 2025 scaling research showed multi-agent coordination can degrade performance on genuinely agentic tasks once a single-agent baseline exceeds ~45% accuracy, sharpening this file's "use composable single-agent patterns over heavyweight multi-agent frameworks" guidance with measured thresholds (Google Research / MIT, 2025; see `agent-systems-research-frontier.md`).
- **Guardrails hardened around prompt injection.** The "lethal trifecta" framing (June 2025) and confirmed indirect-injection exploits (GitHub MCP, malicious MCP packages) made the Guardrails and Self-Check patterns structural necessities for tool-calling and RAG apps, not optional layers (Simon Willison / HiddenLayer, 2025-2026).
- **Prompt Optimization and evaluation got stronger tooling.** DSPy matured for programmatic prompt/pipeline optimization, and LLM-as-Judge plus trajectory evaluation gained standardized observability via OpenTelemetry GenAI Semantic Conventions, LangSmith, and Langfuse (OpenTelemetry / ecosystem, 2025-2026).
- **Context engineering became a named discipline complementing these patterns.** Anthropic (September 2025) and LangChain (July 2025) formalized context engineering (write/select/compress/isolate, memory tools, just-in-time retrieval), giving the "engineer the context, not the instruction" principle here concrete techniques (Anthropic / LangChain, 2025).

Sources: [Flagship model report (Vellum)](https://www.vellum.ai/blog/flagship-model-report), [One Year of MCP](https://blog.modelcontextprotocol.io/posts/2025-11-25-first-mcp-anniversary/), [What is GraphRAG? (IBM)](https://www.ibm.com/think/topics/graphrag), [The lethal trifecta (Willison)](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/), [Effective context engineering (Anthropic)](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents).
