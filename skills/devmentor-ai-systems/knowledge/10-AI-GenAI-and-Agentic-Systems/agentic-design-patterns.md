# Agentic Design Patterns

**Domain:** 10 - AI, GenAI, and Agentic Systems
**Primary sources:** *Agentic Design Patterns: A Hands-On Guide to Building Intelligent Systems* (Gulli, 2025); *Agentic Design Patterns: A Hands-On Guide to Building* (Tiwary, 2025 - companion edition of the same 21-pattern catalog); *Design Patterns for Agentic AI* (Jewell & Li / InfoQ webinar, 2025)

## Overview

An AI agent is a system that perceives its environment and takes actions to achieve a goal. It is an evolution of a plain large language model (LLM), enhanced with the ability to plan, use tools, remember, and interact with its surroundings. A useful mental model is a five-step loop: get the mission, scan the scene (gather context), think it through (plan), take action (execute via tools), and learn and get better (adapt). The field has moved quickly from single prompts, to retrieval-augmented generation (RAG), to single tool-using agents, and now to collaborative multi-agent systems.

Agentic design patterns are the solidifying, reusable building blocks for these systems. Because the underlying models change fast, the durable value sits in these patterns: reflection, tool use, planning, routing, memory, guardrails, and the rest. They are foundational because LLMs are stateless, stochastic, slow, and disconnected from the world; the patterns are the scaffolding that turns an unreliable token predictor into a dependable system. This topic catalogs the patterns precisely so an agent (or an engineer) can select and combine them deliberately. It is the centerpiece of the domain; the companion file `agentic-architecture-patterns.md` covers how multiple agents are organized at the system level.

A spectrum of agent complexity frames where patterns apply:
- **Level 0 - core reasoning engine:** an LLM alone, no tools or memory; strong at explaining known concepts, blind to current events.
- **Level 1 - connected problem-solver:** the LLM uses tools (search, RAG, APIs) to act across multiple steps.
- **Level 2 - strategic problem-solver:** adds planning, proactive assistance, context engineering, and self-improvement.
- **Level 3 - collaborative multi-agent systems:** teams of specialized agents coordinated like a human organization.

## Core concepts

- **Agent vs LLM:** an LLM is a stateless text generator; an agent wraps it with goals, perception (sense), reasoning, planning, action (tools), memory, and coordination in a continuous loop.
- **Context engineering:** the discipline of selecting, packaging, and managing the most relevant information for each step so the model's limited attention is not overloaded. Distinct from prompt engineering and increasingly the core skill of capable agents.
- **Statelessness and the assembled context:** LLMs have no recall between calls. Every agent turn assembles context from many sources (working memory, long-term memory, retrieval, tools, events). Pattern selection is largely about how that context gets built and controlled.
- **Determinism vs stochasticity:** the same prompt does not yield the same output. Agentic engineering means "controlling chaos, not eliminating it" - rely on deterministic workflows where possible and design explicitly for uncertainty everywhere else (Jewell & Li).
- **Structured output as glue:** patterns that pass data between steps (chaining, planning, multi-agent handoffs) depend on structured formats (JSON, Pydantic schemas, JSON mode) rather than free-form natural language, which is brittle.

## Frameworks & models

- **The 21-pattern catalog (Gulli/Tiwary):** Prompt Chaining, Routing, Parallelization, Reflection, Tool Use, Planning, Multi-Agent Collaboration, Memory Management, Learning and Adaptation, Model Context Protocol (MCP), Goal Setting and Monitoring, Exception Handling and Recovery, Human-in-the-Loop, Knowledge Retrieval (RAG), Inter-Agent Communication (A2A), Resource-Aware Optimization, Reasoning Techniques, Guardrails/Safety, Evaluation and Monitoring, Prioritization, Exploration and Discovery. Each is documented in the Patterns section below.
- **Producer-Critic (Generator-Critic) model:** the canonical implementation of Reflection - one agent produces, a second agent with a distinct persona critiques, and feedback loops back to the producer. Separating the roles avoids the cognitive bias of an agent reviewing its own work.
- **ReAct (Reason + Act):** interleaves explicit reasoning steps with tool actions and observations, letting an agent gather information and adapt its plan mid-task. Part of the broader Reasoning Techniques family alongside Chain-of-Thought and Tree-of-Thought.
- **The agentic interoperability stack (Jewell & Li; Gulli ch. 10/15):** three layers - function calling (LLM triggers a local tool), tool protocols / MCP (discover and invoke external tools as portable services), and agent-to-agent / A2A (delegate structured tasks between independent agents). MCP is vertical integration (agent to tools); A2A is horizontal integration (agent to agent).
- **The agentic enrichment loop (Jewell & Li):** the single-agent cycle of prompt -> retrieve -> enrich -> act -> repeat, gathering tool and human feedback to augment the prompt before each LLM call.

## Patterns

For each: intent; when to use; trade-offs. Drawn from Gulli (2025) and Tiwary (2025); the InfoQ webinar (Jewell & Li) is noted where it adds a workflow framing.

### Prompt Chaining (Pipeline)
- **Intent:** decompose a complex task into a sequence of smaller sub-tasks, where each step's output feeds the next, improving reliability and control through divide-and-conquer.
- **When to use:** a task is too complex for a single prompt; it has multiple distinct processing stages; it needs tool interaction between steps; or you are building multi-step reasoning that must maintain state. Jewell & Li: "write a blog then translate to French."
- **Trade-offs:** improves modularity, debuggability, and accuracy of each step; but adds latency and orchestration; reliability depends on structured output between steps - ambiguous intermediate output breaks downstream prompts. Assign a distinct role/persona per stage.

### Routing
- **Intent:** introduce conditional logic so an agent analyzes an incoming request and dynamically directs control to the most appropriate tool, function, or sub-agent.
- **When to use:** the agent must triage or classify input and choose among multiple distinct workflows (e.g., a support bot splitting sales vs technical vs account questions).
- **Trade-offs:** transforms a rigid linear flow into an adaptive one; routing can be done via LLM, rules, or embedding similarity. Misclassification sends work to the wrong specialist; the router itself is a decision point that must be evaluated.

### Parallelization
- **Intent:** execute independent sub-tasks (LLM calls, tool calls, sub-agents) concurrently to cut overall latency, then aggregate.
- **When to use:** a workflow has multiple independent lookups, computations, or service calls with no inter-dependency (research fan-out, multi-API gathering, multi-modal analysis, A/B option generation, parallel validation). Jewell & Li: run security tests from different points of view, then vote.
- **Trade-offs:** large latency wins for independent work; but requires async/concurrency support, raises cost (more simultaneous calls), and needs a synthesis/aggregation step that is typically sequential.

### Reflection (Producer-Critic / Self-Correction)
- **Intent:** have an agent evaluate its own (or another agent's) output against criteria and iteratively refine it, introducing a feedback loop rather than a one-shot answer.
- **When to use:** quality, accuracy, or completeness matters more than speed and the first draft is often suboptimal - code generation, drafting, planning, fact-checking.
- **Trade-offs:** materially improves output quality; the Producer-Critic split avoids self-review bias. Costs extra LLM calls and latency per iteration; needs a stopping condition to avoid endless loops. Jewell & Li frame it as evaluator-optimizer: a generator with an independent evaluator giving feedback until accepted.

### Tool Use (Function Calling)
- **Intent:** describe external functions ("tools") to the LLM so it can decide when to call one and emit a structured call; an orchestration layer executes it and feeds the result back.
- **When to use:** whenever the agent must break out of the model's internal, static knowledge - real-time data (weather, stock prices), private/proprietary data, precise calculation, code execution, or triggering actions (send email, control devices).
- **Trade-offs:** the single capability that makes an LLM able to act; but each tool is attack surface and failure surface (tool errors, bad arguments), needs schemas and validation, and the model can call the wrong tool or hallucinate arguments.

### Planning
- **Intent:** have the agent first generate a coherent plan - decomposing a high-level goal into an ordered sequence of actionable sub-goals - before executing.
- **When to use:** a request is too complex for a single action or tool and requires interdependent steps in a logical order (research reports, employee onboarding, competitive analysis).
- **Trade-offs:** turns a reactive agent into a strategic executor that can adapt its plan; but plans can be wrong or over-engineered, planning adds a step, and rigid plans need re-planning logic when the environment shifts.

### Multi-Agent Collaboration
- **Intent:** decompose a problem across multiple specialized, cooperating agents, each with the tools and skills for its sub-problem, coordinated via sequential handoffs, parallel workstreams, or hierarchical delegation.
- **When to use:** a task is too complex for one agent and benefits from diverse expertise, parallelism, or a structured multi-stage workflow (complex research, software development, creative production).
- **Trade-offs:** synergy and specialization beyond a single generalist; but coordination overhead, harder debugging, and effectiveness bounded by the reasoning limits of the underlying LLMs. (Topologies and coordination mechanics are detailed in `agentic-architecture-patterns.md`.)

### Memory Management
- **Intent:** implement a dual-component memory - short-term contextual memory in the context window for conversational flow, plus long-term memory in external stores (often vector databases) for persistent, semantically retrievable knowledge.
- **When to use:** any agent that must do more than answer a single question - maintain conversation context, track multi-step progress, personalize via user history, or learn from past successes and failures.
- **Trade-offs:** enables coherence, personalization, and learning; but adds infrastructure (session/state management, a memory service, vector store), retrieval can surface irrelevant context, and context windows still cap short-term recall.

### Learning and Adaptation
- **Intent:** integrate mechanisms (reinforcement learning, self-modification, evolutionary search) so agents refine their knowledge and behavior from new data and interactions rather than staying static.
- **When to use:** dynamic, uncertain, or evolving environments needing personalization, continuous improvement, or autonomous handling of novel situations. Examples cited: Self-Improving Coding Agent (SICA), AlphaEvolve.
- **Trade-offs:** moves agents toward true autonomy; but learning loops are complex, can drift or regress, and need evaluation guardrails to prevent the system from "improving" into worse behavior.

### Model Context Protocol (MCP)
- **Intent:** a universal, open, client-server interface that standardizes how LLM applications discover and invoke external tools, data resources, and prompts - turning bespoke integrations into reusable, interoperable components.
- **When to use:** complex, scalable, or enterprise systems that interact with a diverse, evolving set of tools and data and need dynamic capability discovery without redeploying. For a fixed, small set of functions, direct function calling is sufficient.
- **Trade-offs:** fosters an ecosystem of portable tools and decouples tool hosting from the agent; but adds a protocol layer and server infrastructure and is overkill for simple, static toolsets.

### Goal Setting and Monitoring
- **Intent:** embed explicit, measurable objectives plus a monitoring mechanism that continuously tracks progress and environment state against those goals, creating a corrective feedback loop.
- **When to use:** an agent must autonomously execute a multi-step task, adapt to dynamic conditions, and reliably reach a high-level objective without constant human supervision.
- **Trade-offs:** turns reactive agents into proactive, goal-oriented ones with self-assessment; but requires defining good metrics and monitoring infrastructure, and poorly chosen success criteria mislead the agent.

### Exception Handling and Recovery
- **Intent:** equip agents to anticipate, detect, and recover from failures - tool errors, API/network issues, invalid data - via error detection, logging, retries, fallbacks, reverting to a stable state, self-correction, or escalation.
- **When to use:** any agent deployed in a dynamic, real-world environment where failures are possible and operational reliability matters.
- **Trade-offs:** essential for robustness and dependable production behavior; but adds logic and code paths, and naive retries can amplify cost or loop. (Production fault-tolerance variants are expanded in `agentic-architecture-patterns.md`.)

### Human-in-the-Loop (HITL)
- **Intent:** integrate human judgment into the agent loop via oversight, intervention/correction, feedback for learning, decision augmentation, human-agent collaboration, and escalation policies - augmenting rather than replacing humans.
- **When to use:** high-complexity or high-stakes domains with ethical, legal, or safety weight (finance loan approval, legal sentencing, medical, sensitive content) where full autonomy is not permitted or wise.
- **Trade-offs:** delivers accuracy, accountability, and continuous improvement via feedback; but does not scale (humans cannot review millions of tasks), depends heavily on operator expertise, and raises privacy concerns (data must be anonymized before exposure). Usually combined with automation for scale.

### Knowledge Retrieval (RAG)
- **Intent:** before generating, retrieve relevant external knowledge via semantic search over an embedded, chunked knowledge base and augment the prompt, grounding the answer in verifiable data with citations.
- **When to use:** answers must be current, factual, verifiable, or drawn from private/specialized corpora the model was not trained on; reducing hallucination matters.
- **Trade-offs:** overcomes static training data, reduces hallucination, and enables source citation; but adds a retrieval pipeline (embeddings, vector DB, chunking, reranking), and each step adds noise - the system becomes "doubly stochastic" (retrieval + generation), so retrieval quality bounds answer quality. Core concepts: embeddings, semantic similarity/distance, chunking, vector databases.

### Inter-Agent Communication (A2A)
- **Intent:** an open, HTTP-based protocol that lets distinct agents - even on different frameworks - discover one another (via an Agent Card), delegate structured tasks, and share information synchronously or asynchronously. "SMTP for AI agents."
- **When to use:** orchestrating collaboration between two or more agents, especially across frameworks (ADK, LangGraph, CrewAI), or when an agent must dynamically discover and consume another agent's capabilities.
- **Trade-offs:** enables a modular, scalable, cross-vendor multi-agent ecosystem; but introduces protocol, discovery, and network concerns and is unnecessary for a single self-contained agent.

### Resource-Aware Optimization
- **Intent:** dynamically monitor and allocate compute, time, and cost - typically a Router Agent classifies request complexity and dispatches to a cheap/fast model for simple queries and a powerful model for hard reasoning, optionally with a Critique Agent refining the routing.
- **When to use:** strict API/compute budgets, latency-sensitive apps, resource-constrained or edge hardware, or workflows where tasks have varying resource needs and you must balance quality against cost.
- **Trade-offs:** balances output quality against operational cost; but adds routing/critique machinery and risks under-serving a hard task routed to a weak model.

### Reasoning Techniques (CoT, ToT, Self-Correction, ReAct)
- **Intent:** make the agent's internal problem-solving explicit and structured - Chain-of-Thought (step-by-step), Tree-of-Thought (explore multiple paths), Self-Correction (iterative refinement), and ReAct (interleave reasoning with tool actions).
- **When to use:** problems too complex for a single-pass answer that need decomposition, multi-step logic, tool interaction, or where showing the "work" is as important as the answer.
- **Trade-offs:** more robust, transparent, and capable reasoning; but higher token cost and latency, and exposed reasoning can be verbose or itself contain errors.

### Guardrails / Safety
- **Intent:** a multi-layered defense - input validation, output filtering, behavioral constraints via prompting, tool-use restriction, and HITL oversight for critical decisions - to keep agents safe, ethical, and aligned and to resist jailbreaks.
- **When to use:** any application where agent output can affect users, systems, or reputation - customer-facing bots, content platforms, and sensitive domains (finance, healthcare, legal).
- **Trade-offs:** guides rather than limits utility, building trust and compliance and protecting against harmful or adversarial outputs; but each layer adds latency and can produce false positives that block legitimate work; guardrails must be maintained as threats evolve.

### Evaluation and Monitoring
- **Intent:** a systematic framework to assess ongoing agent performance - accuracy, latency, token/resource use, trajectory analysis (the sequence of actions), and LLM-as-a-Judge for qualitative output - with feedback loops, A/B testing, and drift detection.
- **When to use:** live production deployments where reliability is critical; comparing agent/model versions; regulated or high-stakes domains; or where performance may degrade over time (data/environment drift) and complex agentic behavior needs trajectory-level assessment.
- **Trade-offs:** "a system is only as reliable as its evaluation framework" (Jewell & Li) - essential for trustworthy operation; but building eval sets and judges is costly, heuristic metrics are flawed, and human evals are expensive and inconsistent.

### Prioritization
- **Intent:** let an agent rank competing tasks and goals using criteria such as urgency, importance, dependencies, and resource cost, then act on the highest-value item.
- **When to use:** an agent must autonomously manage multiple, often conflicting tasks under resource constraints in a dynamic environment.
- **Trade-offs:** makes behavior purposeful and resource-aware; but criteria must be well-designed, and mis-weighted priorities cause the agent to neglect important work.

### Exploration and Discovery
- **Intent:** build agents that actively seek new information and identify "unknown unknowns" - often a multi-agent setup emulating the scientific method (generate hypotheses, critique, evolve the best).
- **When to use:** open-ended, complex, or rapidly evolving domains where the solution space is undefined and the goal is to uncover novel hypotheses or insights (scientific research, market analysis, creative generation) rather than optimize a known process.
- **Trade-offs:** can generate genuinely new knowledge and accelerate discovery; but is expensive, less predictable, and harder to evaluate than optimization of a known objective.

### Workflow framings (Jewell & Li, InfoQ)
The webinar maps several of the above onto an event-driven, distributed-systems view and adds these orchestration framings that recur in production agentic systems:
- **Orchestrator-worker:** an orchestrator LLM breaks down a task not known in advance and dispatches sub-tasks to workers, then combines results.
- **Evaluator-optimizer:** a generator produces a solution while a separate evaluator gives feedback until it is accepted (the runtime form of Reflection).
- **Agentic workflows as reliable execution:** treat agent flows as durable workflows (sequential, parallel, event-driven, state-machine, human-in-the-loop) with monitoring, timeouts, retries, and error compensation - making them traceable, auditable, debuggable, and recoverable to a point in time.

## Principles & best practices

- **Decompose before you generate.** Most patterns (chaining, planning, multi-agent, reasoning) are forms of divide-and-conquer that reduce cognitive load on the model and raise reliability.
- **Pass structured data, not prose, between steps.** Use JSON/Pydantic/JSON mode for hand-offs; free-form natural language hand-offs are a primary source of instability.
- **Separate the producer from the critic.** Independent evaluation (Reflection, evaluator-optimizer, second-opinion agents) beats self-review.
- **Engineer context deliberately.** Curate short, focused, powerful context per step; counter the "lost-in-the-middle" degradation of long inputs.
- **Prefer deterministic workflows where you can; design for uncertainty where you cannot** (Jewell & Li). Wrap stochastic steps in validation, retries, and guardrails.
- **Evaluation is the foundation, not an afterthought.** Build synthetic/adversarial/real-world eval sets and make reasoning measurable from the start.
- **Layer certainty:** combine HITL, second-opinion agents, specialization (limit each LLM to one domain), restricted decisioning (finite outcome sets), and more "thinking time" under observed uncertainty.
- **Right-size the pattern.** MCP and A2A are for evolving, multi-tool, multi-agent systems; direct function calling and single agents suffice for fixed, simple scopes.

## Anti-patterns & pitfalls

- **The monolithic mega-prompt:** cramming a multifaceted task into one prompt causes instruction neglect, contextual drift, error propagation, and hallucination. Chain or plan instead.
- **Self-review bias:** an agent grading its own homework misses its own errors; use an independent critic.
- **Free-form hand-offs:** relying on natural-language passing between steps/agents produces brittle, non-parseable results.
- **Unbounded loops:** Reflection, learning, and retry patterns without stopping conditions burn cost and can crash-loop.
- **Pattern over-engineering:** adding MCP, A2A, swarms, or blackboards to a problem a single tool-using agent would solve - overhead exceeds benefit.
- **Naive retries:** repeating a failed action without analyzing the failure (the Retry discipline means adaptive, reasoned retry, not blind repetition).
- **"Vibes" engineering:** treating LLMs as deterministic APIs and shipping without evaluation, monitoring, or guardrails - "if you're looking for vibes, it will be short lived" (Jewell & Li).
- **RAG noise stacking:** poor chunking, embeddings, or reranking compounds with generation stochasticity; weak retrieval guarantees weak answers.

## Decision guidance

How an agent should choose among these patterns:

1. **Single prompt or pattern?** If the task is multi-stage, needs tools between steps, or requires multi-step reasoning -> chain or plan; otherwise a single LLM call may do.
2. **Need the outside world?** -> Tool Use / function calling; if the toolset is large/evolving or cross-system -> MCP.
3. **Need facts the model lacks?** -> RAG. Need verifiability/citations -> RAG with provenance.
4. **Quality-critical output?** -> Reflection (Producer-Critic) or evaluator-optimizer with a stopping condition.
5. **Independent sub-tasks?** -> Parallelization; sub-tasks needing different expertise -> Multi-Agent Collaboration.
6. **Choosing among workflows/tools/agents?** -> Routing; choosing under cost/latency budgets -> Resource-Aware Optimization.
7. **Long-running or stateful?** -> Memory Management + Goal Setting and Monitoring.
8. **Real-world deployment?** -> always add Exception Handling/Recovery, Guardrails, and Evaluation and Monitoring; add HITL for high-stakes or ethically loaded decisions.
9. **Cross-framework or cross-org agents?** -> A2A.
10. **Open-ended, undefined solution space?** -> Exploration and Discovery, often multi-agent.

Most real systems compose several patterns: e.g., a Router dispatches to a Planner, which uses Tool Use and RAG, wrapped in Reflection, Guardrails, HITL escalation, and Evaluation.

## Source attribution

- The 21-pattern catalog, intents, "What/Why/Rule of thumb" framing, the agent-complexity levels (0-3), context engineering, and the Producer-Critic model: *Agentic Design Patterns* (Gulli, 2025), chapters 1-21 and the introduction "What makes an AI system an Agent?". The Tiwary (2025) edition mirrors the same catalog and chapter structure and corroborates each pattern.
- RAG core concepts (embeddings, semantic similarity, chunking, vector DBs), HITL aspects and caveats, MCP/A2A distinction, and Reasoning Techniques (CoT/ToT/Self-Correction/ReAct): Gulli (2025) chapters 14, 13, 10, 15, 17.
- Determinism-vs-stochasticity framing, the LLM->agent->agentic-system progression, the agentic enrichment loop, orchestrator-worker / evaluator-optimizer workflow framings, layers-of-certainty techniques, evaluation-driven development, and the "agentic systems are distributed systems" thesis: *Design Patterns for Agentic AI* (Jewell & Li, InfoQ webinar, 2025).

## Recent developments (2024-2026)

- **The MCP and A2A patterns in this catalog became governed open standards.** MCP moved to the Linux Foundation's Agentic AI Foundation (December 2025) and matured to the 2025-11-25 spec (Streamable HTTP transport, OAuth 2.1 auth, structured output, user elicitation); A2A moved to the Linux Foundation (June 2025) with 150+ backing organizations and five SDKs. The "MCP vertical, A2A horizontal" framing here is now industry consensus (modelcontextprotocol.io / Linux Foundation, 2025).
- **Context engineering became a named, formalized discipline.** Anthropic defined it (September 2025) as "curating and maintaining the optimal set of tokens during inference"; LangChain published a four-strategy framing (write, select, compress, isolate) in July 2025. This file's "engineer context deliberately" principle is now a mainstream practice with concrete techniques: context editing/pruning, just-in-time retrieval, and memory tools (Anthropic / LangChain, 2025).
- **Memory Management gained a persistent-storage pattern.** Anthropic shipped a file-based memory tool (public beta, 2025) supporting CRUD operations outside the context window, and subagents with isolated context windows returning condensed summaries became a documented pattern, sharpening this catalog's Memory Management entry (Anthropic engineering, 2025).
- **Reflection and Evaluation patterns moved into dedicated observability tooling.** OpenTelemetry GenAI Semantic Conventions, LangSmith, Langfuse, and Datadog AI Agent Monitoring (DASH 2025) made trajectory-level evaluation and "evaluation as the foundation" operationally standard rather than aspirational (OpenTelemetry / Langfuse, 2025-2026).
- **Guardrails / Safety patterns hardened around the "lethal trifecta."** Simon Willison's June 2025 framing (private data + untrusted content + external communication) and confirmed indirect-prompt-injection exploits (GitHub MCP, malicious npm MCP packages) made the Guardrails pattern a structural necessity for any tool-using agent, not an optional layer (Simon Willison / HiddenLayer, 2025-2026).
- **Agentic RAG superseded static RAG pipelines.** The Knowledge Retrieval pattern evolved: 2025 work established "agentic RAG," where agents plan multi-step retrieval, reflect on intermediate results, and adapt strategy, plus GraphRAG for relationship-aware retrieval, validating this catalog's composition of RAG with Planning, Reflection, and Routing (IBM / survey literature, 2025).
- **Resource-Aware Optimization became economically central.** With frontier models (GPT-5, Gemini 3, Claude Opus 4.5) and cheap open-weight options (DeepSeek-V3.2) spanning nearly two orders of magnitude in per-token price, the Router-based cost/quality pattern here is now a primary production design axis (Vellum, 2025-2026).

Sources: [Effective context engineering (Anthropic)](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents), [Context engineering for agents (LangChain)](https://www.langchain.com/blog/context-engineering-for-agents), [The lethal trifecta (Willison)](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/), [What is GraphRAG? (IBM)](https://www.ibm.com/think/topics/graphrag), [One Year of MCP](https://blog.modelcontextprotocol.io/posts/2025-11-25-first-mcp-anniversary/).
