# GenAI on Cloud Platforms

**Domain:** 10 - AI, GenAI, and Agentic Systems
**Primary sources:** Generative AI for Cloud Solutions (Karuparti & Singh, 2024); Google Machine Learning and Generative AI for Solutions Architects (Kavanagh, 2024); Azure OpenAI Essentials (Mukherjee & Saladi, 2025)

## Overview
Architecting a generative AI (GenAI) solution on a hyperscale cloud is less about the model and more about everything built *around* the model. A large language model (LLM) is an engine; turning it into a dependable product requires grounding it in trusted data, customizing it to a domain, orchestrating it with application code, evaluating its output, scaling it within hard service limits, and operating it under governance. This topic covers the durable reference architectures (RAG, fine-tuning, orchestration), the AI service landscape on Azure and Google Cloud (with AWS noted), how to select and host models, vector databases and grounding, evaluation, and cost and scaling. It matters across the SDLC because GenAI shifts a large share of "correctness" from deterministic code into probabilistic model behavior, data pipelines, and prompt design, which changes how teams build, test, deploy, and run software.

The recurring lesson across all three books: start from business requirements, pick the *least customization that works* (prompt engineering, then RAG, then fine-tuning), keep the model stateless and push state/context into your own architecture, and treat the LLM lifecycle (LLMOps) as iterative, not linear.

## Core concepts

**Foundation models and transformers.** Modern GenAI rests on the transformer architecture (Vaswani et al., *Attention is All You Need*, 2017), which replaced RNNs/CNNs with an attention mechanism that weighs the relevance of every token to every other token, processed in parallel. Three structural variants matter for selection: encoder-only (BERT; understanding tasks like classification, NER), decoder-only (GPT; generative tasks), and encoder-decoder (T5; translation, summarization). A foundation model is a large, self-supervised, pre-trained model that gains a statistical understanding of language but is not aligned to a specific task until customized. (Karuparti & Singh; Mukherjee & Saladi)

**Tokens, context window, and limits.** LLMs operate on tokens (~4 chars or ~3/4 of a word in English). The context window bounds prompt + completion (for example GPT-4 Turbo: 128K context, 4,096 output). Tokens drive both cost and the architectural need for chunking, summarization, and memory. (Karuparti & Singh)

**Hallucination.** Output that is fluent and confident but factually wrong. Distinguished into closed-domain (contradicts provided source material; mitigable by grounding and verification) and open-domain (no source to check against; much harder). The three primary mitigations are fine-tuning, retrieval-augmented generation (RAG), and prompt engineering. (Karuparti & Singh; Ots)

**Grounding.** Constraining model responses to trusted, supplied data so output is verifiable. Implemented through RAG and surfaced as managed features (Azure OpenAI "On Your Data"; Vertex AI grounding against Vertex AI Search). Groundedness is also a measurable evaluation metric. (Mukherjee & Saladi; Kavanagh)

**The customization ladder.** Recommended order of escalating effort and cost: (1) prompt engineering and prompt chaining, (2) function calling, (3) RAG to inject external context, (4) fine-tuning to bake in behavior. RAG is generally more cost-effective than fine-tuning for keeping knowledge current because it retrieves only relevant context per query; fine-tuning is for changing *behavior, format, and style*, not for adding fresh facts. The two can be combined. (Mukherjee & Saladi; Karuparti & Singh)

## Frameworks & models

### Reference architecture: Retrieval-Augmented Generation (RAG)
The dominant pattern for grounding LLMs in enterprise data. Pipeline:
1. **Ingest and chunk** source documents (fixed-size with ~10-15% overlap, or variable/sentence/specialized/code chunking) to fit embedding token limits and preserve semantic coherence.
2. **Embed** each chunk with an embedding model (for example text-embedding-3-large/small or ada-002; Vertex/PaLM embeddings) into high-dimensional vectors.
3. **Index** vectors in a vector database using an ANN index (HNSW, FAISS/IVF).
4. **Retrieve** at query time: embed the user query, run similarity search (cosine, Euclidean/L2, or Manhattan/L1), optionally apply hybrid search (keyword + vector) and semantic re-ranking (BM25/RRF + deep re-ranker).
5. **Augment and generate**: attach retrieved context to the prompt and send to the LLM, which synthesizes a grounded answer.

RAG addresses the LLM's fixed knowledge cutoff, lack of personal memory, and hallucination tendency without retraining. Business applications: enterprise search, legal/compliance lookup, e-commerce, customer support, healthcare. (Karuparti & Singh, ch. 4; Kavanagh, ch. 16-18; Mukherjee & Saladi, ch. 4)

### Reference architecture: Fine-tuning and PEFT
Fine-tuning refines a pre-trained model's weights on a curated, task-specific dataset. Benefits: reduced hallucination on trusted data, better task performance, shorter prompts (lower cost/latency), and consistent output. Full fine-tuning updates all parameters (high compute/memory, catastrophic forgetting, a full model copy per task). Parameter-efficient fine-tuning (PEFT) trains a small parameter subset and is categorized as selective, additive (for example prompt tuning with soft prompts), or reparameterization (for example LoRA). Azure OpenAI fine-tuning is supervised fine-tuning (distinct from continuous pre-training or RLHF); RLHF aligns base models to human values. (Karuparti & Singh, ch. 3; Mukherjee & Saladi, ch. 3)

### Reference architecture: Orchestration
LLMs need application code "built around the engine." Orchestration frameworks (Semantic Kernel, LangChain, LlamaIndex) coordinate prompts, models, plugins/tools, memory, and chains; cloud orchestration features (Azure AI Foundry / Prompt Flow; Vertex AI Search and Conversation) abstract chunking/embedding/indexing. Multi-agent orchestration (AutoGen, AutoGPT, TaskWeaver, Assistants API) lets specialized agents collaborate in hierarchies, networks, or swarms. (Karuparti & Singh, ch. 6; covered in depth in `building-genai-applications.md`)

### The LLM lifecycle and LLMOps
LLMOps manages the production lifecycle of GenAI models: data preparation, discover-and-tune (experimentation, evaluation/refinement), deployment (validation, A/B testing, inferencing/serving via REST, CI/CD), monitoring with human feedback (quality metrics, data drift, infra latency/throughput), and retraining. The lifecycle is iterative across three phases (understand requirements and explore models; build and augment with RAG/prompting/fine-tuning; operationalize and monitor) plus an emerging end-of-life phase for decommissioning and migrating to newer models. (Karuparti & Singh, ch. 6)

### The Azure AI service landscape
- **Azure OpenAI Service (AOAI):** hosted GPT, embeddings, DALL-E, multimodal; deployments per region/model; "On Your Data" for managed RAG; function calling; Assistants API (stateful, code interpreter, file search); Batch API; supervised fine-tuning. Stateless models; pay-as-you-go (TPM) or provisioned (PTU).
- **Azure AI Search:** vector + hybrid + semantic re-ranking, native HNSW indexing; common RAG retrieval store.
- **Azure AI Foundry / Prompt Flow:** orchestration, evaluation, and LLMOps tooling.
- **Cosmos DB / other stores:** vector storage options.
(Mukherjee & Saladi; Karuparti & Singh)

### The Google Cloud / Vertex AI landscape
- **First-party models:** Gemini (Ultra/Pro/Nano, multimodal), PaLM 2 (Text/Chat/Embeddings), Codey (code), Imagen (image), MedLM (medical); Gemma open-weight lightweight models.
- **Vertex AI Model Garden:** one-stop catalog of foundation, open source (Gemma, Llama 2), and third-party (Anthropic Claude) models; Hugging Face partnership.
- **Vertex AI Studio:** UI/SDK to prompt models, tune parameters (temperature, top-K, top-P, output token limit), and enable grounding.
- **Vertex AI Search and Conversation:** managed search/RAG that performs chunking, embedding, and indexing for you (first choice for RAG on GCP), with data connectors (Jira, Salesforce, Confluence).
- **Vector database options:** Vertex AI Vector Search (managed, granular control), BigQuery Vector Search (SQL-native, ANN or brute force), Spanner Vector Search (globally consistent, KNN), AlloyDB AI, plus most GCP databases. Choose by existing data gravity and latency needs.
- **Vertex AI MLOps:** Pipelines, Feature Store, Model Registry, prediction service, Vizier (hyperparameter tuning), lineage tracking.
(Kavanagh, ch. 3, 17, 18)

### AWS (noted)
AWS Bedrock is referenced as the comparable managed LLM platform; the shared three-tier hosting model applies equally. (Ots)

## Patterns

- **Retrieval-Augmented Generation (RAG)** - Intent: ground LLM answers in trusted, current external data without retraining. When to use: knowledge changes faster than training cycles; answers must be verifiable; per-query relevance matters; cost sensitivity. Trade-offs: adds ingestion/chunking/embedding/indexing complexity and a vector store to operate; retrieval quality gates answer quality; chunking strategy and embedding model choice are load-bearing.
- **Fine-tuning / PEFT** - Intent: change a model's behavior, format, tone, or task competence by updating weights. When to use: stable domain behavior is needed, prompts are too long/repetitive, or consistent specialized output matters. Trade-offs: upfront data curation and compute; risk of catastrophic forgetting; does not keep facts current (use RAG for that); PEFT cuts cost/memory versus full fine-tuning at near-equivalent quality.
- **Hybrid + semantic re-rank retrieval** - Intent: combine keyword and vector search, then re-rank by deep language models for relevance. When to use: pure vector recall misses exact-term matches or precision is critical. Trade-offs: extra ranking latency/cost; tune chunk size (512 tokens, ~10% overlap is a common sweet spot).
- **ANN index selection (HNSW vs FAISS/IVF)** - Intent: trade a little accuracy for large speed gains in similarity search. When to use HNSW: high precision, dynamic/frequently updated data, real-time text search. When to use FAISS/IVF + product quantization: very large (billions) mostly-static datasets, GPU batch, flexible speed/accuracy trade-offs. Trade-offs: ANN sacrifices exact nearest neighbors; quantization saves storage at accuracy cost.
- **Similarity-measure selection** - Intent: match distance metric to data. Cosine when direction/orientation matters and magnitude does not (text); Euclidean/Manhattan when absolute differences matter (numeric, spatial). Trade-offs: wrong metric degrades retrieval relevance.
- **Vector library vs vector database** - Intent: avoid over-engineering. Use a library (FAISS) for small, static, in-memory data; use a database for millions/billions of changing records needing CRUD, replication, disk persistence, and ms latency. Trade-offs: libraries lack CRUD/replication/persistence.
- **Multi-account/PTU + APIM scaling ("spillover")** - Intent: exceed per-resource model quota and meet SLAs. When to use: enterprise-scale traffic beyond a single AOAI account's TPM. Method: stack multiple AOAI accounts/regions behind a single API Management instance; route to provisioned (PTU) capacity first, spill over to pay-as-you-go (TPM); use Premium APIM for multi-region/DR. Trade-offs: added APIM cost and config; centralizes cross-cutting concerns.
- **Retries with exponential backoff (the "scaling special sauce")** - Intent: absorb transient failures and 429/5xx rate-limit errors gracefully at scale. When to use: any cloud LLM call, especially high-concurrency. Method: APIM retry policy on 429 and >=500 with growing wait intervals and a max retry count. Trade-offs: hides transient errors but can mask sustained capacity shortfalls; pair with monitoring.
- **Caching + rate limiting for cost control** - Intent: cut token spend and protect backends. When to use: repeated/similar prompts, bursty traffic. Method: Azure Cache for Redis for response caching; APIM for rate limiting. Trade-offs: cache staleness; tuning effort.
- **LLM-as-a-judge evaluation** - Intent: automate large-scale quality assessment of RAG/LLM output. When to use: manual ground-truth comparison does not scale. Method: Prompt Flow metaprompt templates scoring groundedness, relevance, retrieval score, plus custom metrics; supplement with ROUGE/BLEU/F1. Trade-offs: judge model can itself err; use a mix of metrics.

## Principles & best practices
- Start from business requirements and choose the least customization that works (prompt -> function calling -> RAG -> fine-tune). (Mukherjee & Saladi)
- Treat the model as stateless; push state, memory, and context into your architecture (chat history store, vector memory, RAG). (Mukherjee & Saladi; Karuparti & Singh)
- Preprocess before chunking (remove stop words, special symbols, duplicates); test chunk size and overlap empirically (512 tokens / ~10% overlap is a strong default). (Karuparti & Singh)
- Use trusted model and dataset sources (Hugging Face ratings/leaderboards, SOC 2); validate integrity to avoid training-data poisoning. (Karuparti & Singh)
- Evaluate continuously and with multiple metrics; monitor data drift and infra latency/throughput (TPS/RPM). (Karuparti & Singh)
- Design for scale and DR from the start: multi-region, PTU+TPM mix, single APIM serving many accounts, exponential backoff. (Karuparti & Singh)
- Pick the vector store by data gravity and latency needs rather than defaulting to a standalone DB; on GCP, prefer the managed Vertex AI Search and Conversation unless you need control over chunking/embedding/indexing. (Kavanagh)
- The LLM lifecycle is iterative, not linear; expect to loop back from operate to build. (Karuparti & Singh)

## Anti-patterns & pitfalls
- **Fine-tuning to add fresh facts** - it bakes in behavior, not current knowledge; use RAG. (Mukherjee & Saladi)
- **Skipping evaluation / no groundedness check** - undetected hallucinations propagate downstream, including into other models' training data. (Ots; Karuparti & Singh)
- **One giant prompt instead of RAG** - stuffing all context wastes tokens and money; RAG retrieves only what is relevant. (Mukherjee & Saladi)
- **Ignoring rate limits and quota math** - 429 storms and SLA breaches; model TPM/RPM and PTU minimums explicitly and use backoff. (Karuparti & Singh)
- **Over-engineering the data layer** - a vector DB for a tiny static corpus where a library suffices; or a standalone DB when your existing data store already offers vector search. (Karuparti & Singh; Kavanagh)
- **Wrong similarity metric or poor embeddings** - silently degrades retrieval; embedding quality caps the whole RAG system. (Karuparti & Singh)
- **No DR / single-region single-account** - capacity and availability ceilings under enterprise load. (Karuparti & Singh)

## Decision guidance
- **Need current/enterprise knowledge in answers?** -> RAG (and grounding features). **Need consistent behavior/format/tone or shorter prompts?** -> fine-tune (PEFT first). **Both?** -> combine. **Just better steering?** -> prompt engineering first.
- **Model family by task:** understanding/classification -> encoder (BERT); generation/chat -> decoder (GPT, Gemini, PaLM Chat); translation/summarization -> encoder-decoder (T5). Multimodal needs -> Gemini, GPT-4o, multimodal AOAI.
- **Vector store:** small static -> library (FAISS); production scale -> vector DB. On Azure -> AI Search (HNSW, hybrid, re-rank). On GCP -> Vertex AI Search and Conversation (managed) unless you need chunk/embed/index control (Vertex AI Vector Search), SQL-native (BigQuery), or global consistency (Spanner).
- **Index/metric:** HNSW for dynamic high-precision text; FAISS/IVF+PQ for huge static GPU batch. Cosine for text similarity; Euclidean/Manhattan for numeric/spatial.
- **Scaling/cost on Azure:** predictable high load -> PTU; bursty/variable -> TPM pay-as-you-go; exceed quota -> multiple accounts behind APIM with spillover; add Redis cache + APIM rate limiting; always add exponential-backoff retries and plan multi-region DR.
- **Hosting model:** SaaS (Copilot/ChatGPT) for least control/most built-in security; PaaS (Azure OpenAI, Bedrock, Vertex) for shared responsibility and configurable controls; IaaS/self-hosted (KAITO/Kubernetes) only with a mature cloud security operating model. (Ots)

## Source attribution
- Transformers, fine-tuning/PEFT, RAG, vector DBs, chunking, prompt engineering, LLMOps, scaling (TPM/RPM/PTU, APIM, backoff, caching), evaluation: Generative AI for Cloud Solutions (Karuparti & Singh, 2024), ch. 2-7.
- Google Cloud / Vertex AI landscape, Gemini/PaLM/Gemma, Model Garden, Vertex AI Search and Conversation, GCP vector databases, MLOps: Google Machine Learning and Generative AI for Solutions Architects (Kavanagh, 2024), ch. 3, 11, 16-18.
- Azure OpenAI service model, On Your Data RAG, embeddings, function calling, Assistants/Batch APIs, supervised fine-tuning, customization ladder: Azure OpenAI Essentials (Mukherjee & Saladi, 2025), ch. 1-4, 13.
- Hosting/shared-responsibility model context: Securing Microsoft Azure OpenAI (Ots, 2025), ch. 1.

## Recent developments (2024-2026)

- **The hosted-model landscape advanced sharply across all three clouds.** Azure OpenAI now serves GPT-5 (August 2025, with a "thinking-budget" reasoning parameter) and the o-series; Google Vertex AI ships Gemini 3 Pro (November 2025) and Gemma open-weight models; AWS Bedrock and Vertex Model Garden host Anthropic Claude Opus 4.5 (November 2025) and open-weight DeepSeek-V3.2. Headline benchmark spreads compressed to a few points while per-token pricing diverged toward ~2 orders of magnitude, making model routing and cost control more important than picking "the best" model (Vellum / vendor releases, 2025-2026).
- **Every hyperscaler added a managed agent layer on top of the RAG/orchestration stack.** AWS Bedrock AgentCore, Google Vertex AI Agent Builder, and Azure AI Foundry agent services extend the orchestration reference architecture here with managed multi-agent execution, tool/identity, and governance, all interoperating via MCP and A2A (vendor docs, 2025-2026).
- **MCP and A2A became the portability standards the cloud chapters lacked.** MCP moved to the Linux Foundation / Agentic AI Foundation (December 2025) with an official registry; A2A moved to the Linux Foundation (June 2025). All three clouds now support MCP for tool access, reinforcing the "keep the model stateless, push context into your architecture" principle with a standard tool interface (Linux Foundation, 2025).
- **RAG matured into agentic and graph-aware retrieval.** Microsoft GraphRAG and "agentic RAG" (planned multi-step retrieval with reflection) became mainstream, and managed services (Azure AI Search, Vertex AI Search) added stronger hybrid/semantic and graph capabilities, extending this file's RAG reference architecture (IBM / Microsoft, 2024-2025).
- **Observability standardized on OpenTelemetry GenAI Semantic Conventions.** This gives the LLMOps monitoring and evaluation guidance here a vendor-neutral telemetry standard for traces, token usage, and cost across Azure, GCP, and AWS deployments (OpenTelemetry, 2025-2026).
- **Regulation became a hard deployment constraint on cloud GenAI.** EU AI Act GPAI obligations took effect August 2025 (high-risk applicability August 2026; the GPAI Code of Practice published July 2025), making region selection, data residency, and provider compliance posture first-order architecture inputs (EU Commission, 2025-2026).

Sources: [Flagship model report (Vellum)](https://www.vellum.ai/blog/flagship-model-report), [Vertex AI Agent Builder guide](https://uibakery.io/blog/vertex-ai-agent-builder), [One Year of MCP](https://blog.modelcontextprotocol.io/posts/2025-11-25-first-mcp-anniversary/), [What is GraphRAG? (IBM)](https://www.ibm.com/think/topics/graphrag), [EU AI Act timeline](https://artificialintelligenceact.eu/implementation-timeline/).
