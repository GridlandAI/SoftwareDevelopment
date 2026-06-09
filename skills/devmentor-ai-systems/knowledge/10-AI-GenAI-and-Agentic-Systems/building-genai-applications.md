# Building GenAI Applications

**Domain:** 10 - AI, GenAI, and Agentic Systems
**Primary sources:** Building AI Applications with Microsoft Semantic Kernel (Meyer, 2024); Azure AI Services Succinctly (Del Sole, 2025); with supporting material from Azure OpenAI Essentials (Mukherjee & Saladi, 2025) and Generative AI for Cloud Solutions (Karuparti & Singh, 2024)

## Overview
Building a GenAI application means wrapping a stateless, probabilistic model in deterministic application code that gives it tools, memory, and structure. Two complementary toolkits dominate the Microsoft ecosystem: orchestration frameworks (Semantic Kernel and peers) that coordinate prompts, functions/plugins, planners, and memory into multi-step AI workflows; and pre-built AI services (Azure AI Services: vision, language, speech, document, content safety) that provide turnkey intelligence behind REST APIs and SDKs. This topic covers how to compose these into working applications: the kernel/plugin/planner/memory model, function calling, RAG inside an application, multi-agent collaboration, and the integration patterns for consuming Azure AI Services.

Why it matters in the SDLC: GenAI apps move logic out of hand-written control flow and into model-driven planning and tool selection. That is powerful (users combine capabilities you never coded explicitly) but it changes design, testing, and error handling. The frameworks exist to make this tractable: reusable functions, centralized configuration, separation of prompt from code, and graceful degradation when the model misbehaves.

## Core concepts

**Models vs applications.** A model (GPT-4, Gemini) generates text/images; an application (ChatGPT, Copilot, your own) wraps the model with state management, tools, UI, and business logic. The model is the engine; the framework is the assembly line. (Meyer, ch. 1; Karuparti & Singh)

**Semantic Kernel (SK).** A lightweight, open-source SDK (C#, Python) that originally powered Microsoft Copilots, then was released to developers. Everything revolves around the **kernel**: a lightweight repository of the services (model connectors) and plugins available to your app. Every prompt or function call passes through the kernel, which selects the AI service, renders the prompt template, dispatches it, processes the response, and provides hooks for events/middleware (logging, responsible-AI checks) from one place. The fundamental building blocks for building agents are **plugins, planners, and personas**. (Meyer; Karuparti & Singh, ch. 6)

**Functions: semantic vs native.**
- **Semantic functions** are prompts with configuration. In SK they live in a plugin directory as `skprompt.txt` (the prompt template, with `{{$input}}` variables) plus `config.json` (model settings and, importantly, a `description` the planner reads).
- **Native functions** are regular code (C#/Python) decorated to expose them to the kernel (`[KernelFunction]` / `@kernel_function`) with `Description` attributes on the function and its parameters. Loading a native function into the kernel makes it available to chaining and to the planner.
A **plugin** is a collection of functions (formerly called a "skill"); you cannot mix native and semantic functions in one plugin definition. (Meyer, ch. 1, 3)

**Chaining.** The output of one function feeds the input of the next, combining semantic and native functions into pipelines (for example: generate a story -> split into parts -> generate a DALL-E image per part). This is the manual alternative to a planner. (Meyer, ch. 4; LangChain's core idea per Karuparti & Singh)

**Memory and embeddings.** Because models are stateless and context windows are bounded, applications add memory via **semantic memory search**: text is stored as embedding vectors; retrieval is by similarity, not by key. This lets an app recall context it was given long ago ("my favorite city is Paris" matches a later "the city I like the most") that a string/key lookup would miss. SK abstracts a memory store (for example `VolatileMemoryStore` for dev; persistent vector DBs for production) plus an embeddings generator. (Meyer, ch. 6)

**Personas / meta prompts.** Instruction sets that shape an agent's character and response style (verbose vs concise, friendly vs formal). (Karuparti & Singh, ch. 6)

## Frameworks & models

### The Semantic Kernel application model
- **Kernel** = services + plugins registry; instantiate empty, then add a chat-completion connector (OpenAI/Azure OpenAI) and import plugins.
- **Plugins** extend capability: native code, semantic prompts, or REST/external service calls. Core plugins ship with SK (for example `TimePlugin`).
- **Planners** turn a user goal into a multi-step plan over the loaded functions (see below).
- **Personas** set behavior.
This is the same orchestration layer Microsoft uses for Microsoft 365 Copilot and Bing Copilot. (Meyer; Karuparti & Singh)

### Planners
A planner is a built-in SK function that reads the descriptions of all loaded functions, parameters, and outputs and decides how to combine them to satisfy a user request, producing a **plan**. Two planners:
- **Function Calling Stepwise planner** (Python and C#): generates a plan as a dialog with the chat service; can be longer / consume more tokens.
- **Handlebars planner** (C#, experimental at time of writing): asks the model to write Handlebars script that calls the functions; supports loops/conditionals; expected to use fewer tokens.

**When to use a planner:** when you want users to combine atomic functions in ways you did not code explicitly, and so that the app improves automatically as models improve (swap GPT-3.5 -> GPT-4 with a config change). **Considerations:** performance (planner must read all function descriptions; richer kernel = slower), cost (long prompts + generated plans bill both ways; cache frequent plans but regenerate on model upgrades), and testing (plans can exceed the context window or hallucinate functions; a simple, effective recovery is to resubmit the failed plan and ask the model to "fix it"). (Meyer, ch. 5)

### Function calling (Assistants API and AOAI)
Beyond SK, the model itself can choose to call developer-defined functions: the app describes available functions; the model decides when to call them and with what arguments; the app executes and returns results. The Azure OpenAI / OpenAI **Assistants API** is the stateful evolution of chat completions: it manages threads (persistent, effectively unlimited conversation state), tool integration (function calling, code interpreter), file access, and retrieval, removing the need to hand-build thread/state management. (Mukherjee & Saladi, ch. 3; Karuparti & Singh, ch. 6)

### RAG inside an application
SK implements RAG by creating a search index, uploading/embedding documents, retrieving by similarity at query time, and summarizing across retrieved results (for example summarizing several academic articles on a topic). Same pipeline as the platform-level RAG, but composed in app code through plugins and memory. (Meyer, ch. 7; see `genai-on-cloud-platforms.md` for the full RAG reference architecture)

### Multi-agent collaboration
Specialized agents collaborate on a task, often under a manager/orchestrator agent. Example: a creative-agency workflow with Trend Analysis, Concept Generation, Design/Visualization, Copywriting, Feedback/Iteration, and a manager Integration/Strategy agent. Frameworks: **AutoGen** (Microsoft Research, template-based multi-agent), **TaskWeaver** (code-first), **AutoGPT** (autonomous task chaining), and the **Assistants API** multi-agent pattern (for example image generation and refinement). Agents can be arranged in hierarchies, networks, or swarms; high-impact actions should keep a human in the loop. (Karuparti & Singh, ch. 6; Mukherjee & Saladi, ch. 10)

### Azure AI Services (pre-built intelligence)
Turnkey AI behind RESTful APIs and SDKs (Azure SDK for .NET; also Python/others). Authenticate per resource (key or, preferably, managed identity). The suite (Del Sole):
- **Azure AI Search:** intelligent search; backbone for RAG retrieval.
- **Azure AI Language:** NLP - text analytics (sentiment, key-phrase, entity/NER), plus LUIS and QnA Maker for intent and Q&A layers.
- **Azure AI Document Intelligence (formerly Form Recognizer):** automated extraction from forms, invoices, receipts, contracts; prebuilt models for common docs, custom models for industry forms, table extraction, multi-language; structured and unstructured.
- **Azure AI Translator:** real-time machine translation for 100+ languages, including whole documents preserving format (Word/PDF/PowerPoint); custom translators for domain tuning; integrates with Blob Storage for document flows.
- **Azure AI Speech:** speech-to-text, text-to-speech (customizable voices), speech translation, speaker recognition.
- **Azure AI Computer Vision:** image analysis (object detection, OCR, content moderation), Face API (gated access for biometrics), Custom Vision (build classification models).
- **Azure AI Content Safety:** filters inappropriate text/image/video; custom categories; underpins responsible-AI guardrails (see `securing-genai-systems.md`).
(Del Sole, ch. 1, 4-9)

## Patterns

- **Kernel-centric orchestration** - Intent: a single, lightweight registry of services + plugins through which every prompt and function call passes, giving one place for service selection, prompt templating, events, logging, and responsible-AI hooks. When to use: any non-trivial SK app. Trade-offs: a layer to learn; richer kernels slow planners.
- **Semantic function (prompt-as-plugin)** - Intent: separate the prompt (`skprompt.txt`) and its config (`config.json`) from calling code so prompts are reusable, versionable, and discoverable by the planner via their `description`. When to use: any reusable prompt. Trade-offs: more files/structure; descriptions must be accurate for the planner to work.
- **Native function (code-as-plugin)** - Intent: expose deterministic code (file parsing, DB writes, API calls) to the kernel/planner with decorators and descriptions. When to use: the model needs to *do* something, not just generate text. Trade-offs: must guard side effects; descriptions drive correct selection.
- **Function chaining** - Intent: hand-built pipeline where each function's output feeds the next, mixing semantic and native steps. When to use: the workflow is known and fixed; you want deterministic control and easy testing. Trade-offs: rigid; you must anticipate every path users need.
- **Planner-driven workflow** - Intent: let the model assemble atomic functions into a plan from a natural-language goal. When to use: open-ended user requests; you want emergent capability and auto-improvement as models upgrade. Trade-offs: latency, token cost, and hard-to-test nondeterminism; needs error handling for hallucinated/oversized plans (resubmit-and-fix works in practice); cache frequent plans but regenerate on model change.
- **Model-driven function calling** - Intent: the model selects and parameterizes developer-defined functions at runtime. When to use: tool use where the model should decide when/how to act. Trade-offs: requires careful function descriptions, argument validation, and output sanitization.
- **Stateful assistant (threads)** - Intent: offload conversation state, tool integration, and retrieval to a managed Assistants API instead of hand-building thread/context management. When to use: multi-turn assistants needing persistent, long threads, code execution, or file search. Trade-offs: vendor-managed state and behavior; less low-level control.
- **Semantic memory** - Intent: give a stateless app long-term recall via embedding-based similarity search instead of key lookups. When to use: personalization, long conversations, knowledge beyond the context window. Trade-offs: embeddings are fixed-length (can be larger than the source text); needs a vector store; retrieval quality depends on embedding model.
- **History summarization** - Intent: keep conversations within the context window by summarizing prior turns. When to use: long chats approaching token limits. Trade-offs: summary loses detail; chain an LLM summarization step.
- **Multi-agent collaboration with a manager** - Intent: decompose complex goals across specialized agents coordinated by an orchestrator. When to use: tasks with distinct sub-skills (research, design, copy, QA). Trade-offs: complexity, cost, and emergent behavior; keep humans in the loop for high-impact actions and add guardrails.
- **Pre-built AI service via SDK** - Intent: consume turnkey vision/language/speech/document/safety capabilities over REST/SDK rather than building models. When to use: standard tasks (OCR, sentiment, transcription, invoice extraction, content moderation). Trade-offs: less control than custom models; authenticate with managed identity, not keys.

## Principles & best practices
- Separate prompt from code (semantic functions); write precise `description` fields because planners and function calling depend on them. (Meyer)
- Keep functions atomic and well-described so planners can recombine them and users get capability you did not hand-code. (Meyer)
- Treat the model as stateless; add memory deliberately (semantic memory for recall, summarization to fit the window, Assistants threads for managed state). (Meyer; Mukherjee & Saladi)
- Plan for planner failure: handle context-window overflow and hallucinated functions; resubmit-and-fix is a cheap effective recovery; cache frequent plans and regenerate on model upgrades. (Meyer)
- Choose SK/LangChain for complex agent/orchestration and chatbots; LlamaIndex for search-focused RAG; SK tends to perform better in large-scale business apps, LangChain has broader open-model compatibility. (Karuparti & Singh)
- Prefer managed identity over API keys when calling Azure AI Services; use the right pre-built service before building a custom model. (Del Sole; Ots)
- Keep humans in the loop for high-impact agent actions and apply content-safety guardrails. (Karuparti & Singh; Mukherjee & Saladi)

## Anti-patterns & pitfalls
- **Vague function descriptions** - planners and function calling pick the wrong tool or hallucinate; descriptions are load-bearing. (Meyer)
- **Planner for everything** - latency and token cost balloon and tests get fragile; use fixed chaining when the workflow is known. (Meyer)
- **Hand-rolling conversation state** when the Assistants API already manages threads/tools/retrieval. (Mukherjee & Saladi)
- **Stuffing all history into the prompt** instead of summarizing or using semantic memory - hits context limits and wastes tokens. (Meyer)
- **Unbounded autonomous agents** with no human-in-the-loop or guardrails on high-impact actions (excessive agency). (Karuparti & Singh; Ots)
- **Building a custom model** for a task a pre-built Azure AI Service already covers (OCR, sentiment, transcription). (Del Sole)
- **Trusting model output blindly** in downstream code (insecure output handling); sanitize before acting. (Karuparti & Singh; Ots)

## Decision guidance
- **Orchestration framework:** complex agentic flows/chatbots -> Semantic Kernel or LangChain; search-centric RAG -> LlamaIndex; large enterprise apps -> SK; broad open-model support -> LangChain.
- **Fixed vs flexible flow:** known workflow -> function chaining (deterministic, testable); open-ended user goals -> planner (flexible, auto-improving, but costlier and harder to test).
- **State:** simple/dev -> VolatileMemoryStore; production recall -> persistent vector memory; multi-turn with tools/files -> Assistants API threads.
- **Single agent vs multi-agent:** one skill -> single agent + tools; distinct sub-skills needing collaboration -> multi-agent with a manager and human-in-the-loop.
- **Build vs buy intelligence:** standard vision/language/speech/document/safety task -> pre-built Azure AI Service over SDK (managed identity auth); novel domain task -> custom model or fine-tuning.
- **Planner choice (SK):** C# wanting compact, loop-capable plans -> Handlebars (experimental); cross-language stepwise reasoning -> Function Calling Stepwise.

## Source attribution
- Semantic Kernel kernel/plugins/semantic+native functions/chaining/planners/memory/embeddings, RAG in an app, multi-agent context: Building AI Applications with Microsoft Semantic Kernel (Meyer, 2024), ch. 1-8.
- Azure AI Services (Search, Language, Document Intelligence, Translator, Speech, Computer Vision, Content Safety), REST/SDK integration and auth: Azure AI Services Succinctly (Del Sole, 2025), ch. 1, 4-9.
- Assistants API, function calling, multi-agent framework, customization ladder: Azure OpenAI Essentials (Mukherjee & Saladi, 2025), ch. 3, 10.
- Orchestration frameworks comparison (SK/LangChain/LlamaIndex), AutoGen/TaskWeaver/AutoGPT, personas, agent concepts: Generative AI for Cloud Solutions (Karuparti & Singh, 2024), ch. 6.

## Recent developments (2024-2026)

- **Semantic Kernel and AutoGen merged into the Microsoft Agent Framework.** Announced in public preview October 2025 and reaching v1.0 (.NET and Python) in April 2026, it combines AutoGen's multi-agent orchestration with Semantic Kernel's enterprise foundations (state, type safety, middleware, telemetry) and adds graph-based workflows. Both predecessor frameworks entered maintenance mode (bug/security fixes only). New SK/AutoGen projects should target the unified framework (Visual Studio Magazine / Microsoft Learn, 2025-2026).
- **The Agent Framework standardizes on MCP and A2A.** v1.0 ships multi-provider support (Azure OpenAI, OpenAI, Anthropic Claude, AWS Bedrock) and interoperability via MCP (tools), A2A (agent-to-agent), and AG-UI, replacing the bespoke plugin/connector model this file describes with open standards (Microsoft Foundry blog, 2025-2026).
- **The Assistants API is being superseded by the Responses API.** OpenAI introduced the Responses API (2025) as the consolidated stateful primitive for tool use, built-in tools, and agent workflows; the Assistants API entered a deprecation path. Apps built on the stateful-assistant/threads pattern here should plan migration (OpenAI platform updates, 2025).
- **Planner-driven workflows converged on graph-based orchestration.** The industry settled on explicit graph/state-machine orchestration (LangGraph, the Agent Framework's workflows) over free-form planners for reliability, matching this file's "fixed chaining when the workflow is known" guidance while adding checkpointing and durable execution (LangChain / Microsoft, 2025-2026).
- **Azure AI Services consolidated under Azure AI Foundry.** Vision, Language, Speech, Document Intelligence, and Content Safety are increasingly accessed through the unified Foundry platform with agent tooling, evaluation, and tracing, and Content Safety added Prompt Shields and groundedness detection as default guardrail layers (Microsoft, 2025).
- **Context engineering and memory tools entered the application toolkit.** Anthropic's file-based memory tool (public beta, 2025) and the context-engineering discipline (write/select/compress/isolate, just-in-time retrieval) extend this file's semantic-memory and history-summarization patterns with first-class platform support (Anthropic / LangChain, 2025).
- **Prompt injection defense became mandatory for tool-calling apps.** The "lethal trifecta" framing (June 2025) and confirmed indirect-injection exploits made guardrails and output sanitization structural requirements for any app where the model calls functions or processes external content (Simon Willison, 2025-2026).

Sources: [Semantic Kernel + AutoGen = Microsoft Agent Framework (VS Magazine)](https://visualstudiomagazine.com/articles/2025/10/01/semantic-kernel-autogen--open-source-microsoft-agent-framework.aspx), [Microsoft Agent Framework 1.0 (VS Magazine)](https://visualstudiomagazine.com/articles/2026/04/06/microsoft-ships-production-ready-agent-framework-1-0-for-net-and-python.aspx), [Microsoft Agent Framework overview (Microsoft Learn)](https://learn.microsoft.com/en-us/agent-framework/overview/), [Effective context engineering (Anthropic)](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents), [The lethal trifecta (Willison)](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/).
