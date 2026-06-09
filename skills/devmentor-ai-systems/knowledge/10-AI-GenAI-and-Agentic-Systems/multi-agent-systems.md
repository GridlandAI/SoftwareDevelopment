# Multi-Agent Systems

**Domain:** 10 - AI, GenAI, and Agentic Systems
**Primary sources:** AI Agent Systems: Architectures, Applications, and Evaluation (Bin Xu, 2026); Build a Multi-Agent System (from Scratch) With MCP and A2A (Val Andrei Fajardo, 2025); AI Agents with MCP (Kyle Stratis, 2025); Building AI Agent Platforms (O'Mahony and Nonnenmacher, 2026)

## Overview

An AI agent couples a foundation model (an LLM or vision-language model) with an execution loop that can observe an environment, plan, call tools, update memory, and verify outcomes. The shift this represents is from a model that *generates text* to a system that *acts*: it translates natural-language intent into procedures carried out in the world (code repositories, browsers, enterprise systems, robots). Most real-world tasks are not single-turn question answering. They involve gathering information from multiple sources, maintaining state over time, choosing among tools, and executing multi-step actions under constraints (latency, permissions, safety, cost). Agents address that gap. Pure conversational systems fail in these settings because of hallucination, lack of grounding, and an inability to execute or verify actions.

A multi-agent system (MAS) combines several specialized agents that collaborate to perform a task. MAS excel when a complex task decomposes into smaller sub-tasks where focused agents outperform a single general-purpose one (a "report writer" agent paired with a "financial-document extractor"; a "frontend coder" paired with a "backend coder"). The recurring lesson across the literature is to treat agents as *budgeted, tool-augmented systems* rather than purely linguistic models, where reliability and governance are properties of the full stack (model plus orchestration plus tools), not of the base model alone. This matters in the SDLC because agentic systems are increasingly the interface between user intent and real computation, and they are hard to build, test, and operate predictably.

## Core concepts

**The agent transformer (formal model).** Bin Xu defines an agent as a tuple A = (policy, memory, tools, verifiers, environment): a transformer policy embedded in a structured control loop with explicit interfaces to (i) observations from an environment, (ii) memory (short-term working context and long-term state), (iii) tools with typed schemas, and (iv) verifiers/critics that check proposals before side effects occur. Agent behavior becomes a sequence model over interaction traces: observations, intermediate thoughts/plans, tool invocations, and outcomes.

**The execution / processing loop.** At each step the agent: (1) collects an observation, (2) retrieves relevant memory, (3) proposes a candidate action with the policy conditioned on the observation and memory, (4) validates the action against verifiers and tool-schema constraints, and (5) executes the chosen tool call, updating both environment and memory. A minimal loop is: retrieve context, plan, act via tools, verify, update memory, repeat. Fajardo frames the same loop as task execution through a series of sub-steps: at the start of each step the agent synthesizes prior progress to form the next intermediate plan, can make tool calls within any step, and can course-correct or exit early when the task is complete.

**Prerequisite LLM capabilities.** Two backbone capabilities make an agent possible (Fajardo): *planning* (formulating an initial plan and adapting it based on results of past steps) and *tool calling* (generating a structured request, usually JSON, naming a tool and its parameters; the agent executes the call elsewhere and feeds the result back). LLMs are taught to tool-call via supervised fine-tuning on instruction examples; a model that cannot reliably emit tool-call requests is a poor backbone choice.

**Why LLMs alone are insufficient.** An LLM can express intent ("I will search the web for highly rated croissants") but cannot act on it. The agent is the orchestration system that turns intent into executed actions and manages the back-and-forth between tool processing and the model, which a user would otherwise have to do by hand.

**Risk-aware, budgeted control.** Actions differ in reversibility and impact, from low-risk read-only queries to high-risk irreversible operations (writes, deployments, payments). A mature agent branches on risk: low-risk actions run with minimal deliberation; high-risk actions trigger additional verification, multi-step evidence gathering, sandboxing, or human confirmation. In this framing, verifiers are not optional add-ons. They define the operational semantics of the agent. A ReAct-style trace is valuable not only for performance but for governance, because it binds decisions to evidence and supports post-hoc auditing and reproducible replay.

**Agent vs agentic workflow.** A useful distinction (Stratis, O'Mahony): in an *agent*, the LLM dynamically directs its own process and tool usage, deciding which tools to use over one or more action-feedback loops. In an *agentic workflow*, code (not the LLM) determines the path the logic takes, while still calling an LLM for parts of the work. Workflows are the most reliable way to run an ordered set of steps. O'Mahony's rule: if the control flow is strictly deterministic and follows a fixed order, favor an orchestrated workflow; for guided, LLM-driven decision-making within a structured flow, use a graph-based agent.

**Memory types.** Episodic memory (what happened), semantic memory (facts), and procedural memory (skills) support coherence beyond the raw context window. Retrieval-augmented generation (RAG) grounds claims in retrieved evidence and reduces hallucination, but memory also increases attack surface (retrieved prompt injection) and introduces consistency problems when stored state conflicts with new observations. Critics and verifiers mitigate this by checking claims against tool outputs and trusted sources.

## Frameworks & models

**Agent modules (the reference decomposition).** Common modules with clear contracts (Bin Xu): an LLM policy core, retrieval/memory, planners, tool routers, and critics/verifiers. Modularization changes *what the core model must learn*. If retrieval is a tool, the model learns query formulation rather than fact memorization. If a verifier exists, the model can propose candidates and rely on checks to reject unsafe actions. This enables a division of labor where smaller specialized models handle routing, safety classification, or summarization, lowering cost and improving predictability.

**Prominent agent patterns unified by the agent-transformer abstraction.**
- **RAG**: makes retrieval a first-class tool and memory operation, grounding the policy in external evidence.
- **ReAct**: interleaves reasoning tokens and tool calls, improving grounding and producing inspectable, auditable traces.
- **MRKL-style routing**: routes tasks to specialized tools, separating language understanding from deterministic components and improving governability.
- **Reflexion / critic-reflection**: adds an internal feedback channel ("verbal reinforcement") that reduces compounding errors and supports iterative repair.
- **Tree-of-Thoughts (search-based deliberation)**: treats planning as exploring a space of candidate actions, trading compute for reliability.

**Agentic-workflow patterns (Anthropic's taxonomy, via Stratis).**
- **Prompt chaining**: a sequence of LLM calls with code deciding next steps (e.g., detect language, branch to a deterministic parser or another LLM call).
- **Parallelization**: several LLM calls at once whose results are combined.
- **Routing**: an LLM classifies an input, then routes to one of several possible LLM calls.
- **Orchestrator-worker**: one LLM breaks down a task, calls worker LLMs on sub-tasks, then an LLM combines results.
- **Evaluator-optimizer**: one LLM generates a response, a second evaluates it and either accepts it or returns feedback for refinement.

**Application patterns for agents (O'Mahony).**
- **Raw AI agent**: autonomously decides control flow and tool invocation. Powerful but unreliable as task complexity grows.
- **Graph-based agent**: the agent's autonomy is intentionally limited to state transitions within a predefined graph (a state machine). This is the established method for more reliable, resilient execution. "Deep Research" modes are the canonical example: the graph ensures the agent systematically completes key stages without doing too little research or looping endlessly.
- **Orchestrated workflow**: control flow lives in an external workflow engine (Airflow, Argo) rather than agent code, gaining checkpointing, error handling, parallel execution, retries, and monitoring out of the box.
- **Composite architecture**: real applications combine these (a graph-based agent with a RAG stage and a chat interface; multiple agents collaborating on sub-tasks).

**Orchestration frameworks (conceptual).** The "glue logic" connecting an LLM to tools is now handled by libraries that offer abstractions for defining tools and automating tool-calling: vendor SDKs (OpenAI Agents SDK, Google Agent SDK) and vendor-agnostic open-source options (LangGraph, Pydantic AI, CrewAI). LangGraph in particular embodies the graph-based-agent model (a state machine of nodes the agent transitions between); CrewAI emphasizes role-based agent teams; the orchestrator-worker pattern underlies "manager plus workers" designs. Fajardo's book builds the equivalent infrastructure from scratch (BaseTool, BaseLLM, processing loop) precisely to demystify what these frameworks do internally.

**Learning mechanisms for agents (Bin Xu's three layers).**
- *Mechanism level*: reinforcement learning (optimizes long-horizon returns but faces sparse rewards, expensive rollouts, and exploration-safety limits; offline and constrained/safe RL help), imitation learning (behavioral cloning from expert traces; cheap and avoids unsafe exploration but inherits demonstration biases and compounds errors, mitigated by DAgger-style correction), in-context learning ("soft programming" of action formats and schemas via prompts and exemplars), and test-time compute (reflection, self-consistency, search).
- *System level*: modular architectures plus infrastructure (schemas, sandboxing, audit logs) that constrain side effects and make behavior inspectable.
- *Model level*: instruction/policy tuning and preference optimization (RLHF, DPO) shape tool-use discipline and refusal behavior; trace-centric fine-tuning teaches agents to *operate* (plan, act, verify, recover), not merely answer.

**Hybrid neuro-symbolic control.** Traditional rule/graph/behavior-tree (RGB) components remain valuable baselines and safety interfaces because they are predictable, inspectable, and auditable. Modern stacks hybridize: LLMs propose goals or candidate actions while RGB components enforce safety, timing, and domain rules (cooldowns in games, approval gates in enterprise workflows, safety envelopes in robotics).

## Patterns

**Planner-executor separation** - Intent: separate plan generation from plan execution so each runs under different permissions and validation. When to use: tasks with high-impact or irreversible steps that benefit from human-in-the-loop approval. Trade-offs: improves controllability and reduces blast radius of failures; adds coordination overhead and latency. *(Bin Xu)*

**Role-specialized multi-agent team (planner / executor / reviewer)** - Intent: decompose a task across agents with distinct roles for cross-checking and specialization. When to use: complex tasks decomposable into sub-tasks where focused agents beat a generalist. Trade-offs: enables specialization and evidence-based cross-checking; introduces coordination cost (latency, tokens) and can amplify inconsistency when agents disagree. Works best when roles have distinct tool access and explicit handoff artifacts (plans, checklists, traces). *(Bin Xu; Fajardo)*

**Bounded-capability roles with permission budgets** - Intent: give each agent role an explicit tool-permission budget so collaboration scales without expanding any single agent's blast radius. When to use: production multi-agent graphs with side-effecting tools. Trade-offs: limits damage from a compromised or erring agent; requires per-role permission design and enforcement. *(Bin Xu)*

**Trace-first data flywheel** - Intent: run the agent in realistic environments, log full trajectories (prompts, tool calls, outputs, outcomes), and mine failures for targeted improvements. When to use: any production agent where reliability must improve continuously. Trade-offs: shifts effort from one-off training to continuous system refinement and makes eval suites first-class engineering artifacts; requires logging infrastructure and storage, with privacy considerations. *(Bin Xu)*

**Adaptive fast-path / verified-path execution** - Intent: route routine cases through a fast path and high-risk actions through slower, verified paths with explicit budgets and permission gates. When to use: tool-rich deployments mixing low- and high-impact actions. Trade-offs: balances latency against safety; adds branching complexity to the orchestrator. *(Bin Xu)*

**Evidence-based disagreement resolution** - Intent: resolve multi-agent disagreement with citations to tool outputs, trace-anchored critiques, and structured voting rather than free-form debate. When to use: multi-agent debate or review settings prone to correlated errors. Trade-offs: reduces correlated failures and amplification of shared blind spots; requires a well-calibrated aggregation mechanism. *(Bin Xu)*

## Principles & best practices

- **Evaluate the agent as a system, not a model.** Report end-to-end success *and* cost/latency, tool-use correctness, trajectory quality, robustness under variability, and safety violations. These determine deployability.
- **Make tools typed.** Tool schemas turn open-ended text into typed actions, enable automatic argument validation before execution, and reduce brittleness when models hallucinate arguments.
- **Decide when to allow side effects.** The most important design choice in tool-rich environments is gating high-impact actions behind stronger verification, human confirmation, or sandboxed execution.
- **Use the smallest model that works for routing and moderation.** Delegate cheap classification, safety checks, and summarization to smaller models.
- **Control context growth.** Cache frequent retrievals, batch model calls, and compress memory via summaries; context growth raises cost and latency and can dilute critical constraints.
- **Treat prompts as versioned artifacts** with eval suites and regression checks; small wording or formatting changes can shift behavior dramatically.
- **Prefer evidence-anchored traces.** Bind decisions to tool outputs to enable auditing, debugging, and reproducible replay.
- **Set wrong initial plans cheaply.** A bad plan formed at the start can cause catastrophic outcomes (wrong results, timeouts, massive inefficiency); plan adaptation throughout execution is essential (Fajardo).

## Anti-patterns & pitfalls

- **Treating agents as deterministic software.** They are non-deterministic and probabilistic; open-ended output makes traditional unit tests unsuitable, so systematic evaluation is required instead.
- **Free-form multi-agent debate among agents with shared blind spots.** Debate can amplify rather than correct errors if the aggregation is poorly calibrated.
- **Pure behavioral cloning.** Inherits demonstration biases and coverage gaps (organization-specific habits, missing edge cases, no adversarial inputs) and compounds errors under distribution shift.
- **Unbounded in-context growth.** Long prompts dilute constraints, raise cost, and widen the prompt-injection attack surface.
- **Trusting the final response only.** Safety and correctness must be evaluated along the whole trajectory, not just the terminal answer.
- **Single "God" agent.** A single agent tasked with an entire valuable, complex domain (HR to logistics) struggles to choose correct actions; specialized subsystems bring error cases under control far better (O'Mahony).
- **Multi-agent for its own sake.** Multi-agent and graph-based patterns are powerful only for specific use cases; they carry significant complexity and overhead and should be justified by the problem, not the technology (O'Mahony).

## Decision guidance

- **Agent vs workflow vs single LLM call.** If deterministic software (or an `if` statement) solves it, do that. If the control flow is fixed and ordered, use an orchestrated workflow. If the task needs LLM-driven decisions inside a known structure, use a graph-based agent. Reserve a raw, fully autonomous agent for genuinely open-ended tasks where the path cannot be predetermined.
- **Single vs multi-agent.** Stay single-agent until a task clearly decomposes into sub-tasks where specialized agents outperform a generalist and the coordination cost is justified. Multi-agent buys specialization and cross-checking at the price of latency, token usage, and consistency risk.
- **How much deliberation.** Add deliberation depth (tree search, self-consistency, explicit critics) for hard or high-impact tasks; keep routine tasks on a fast path. Verification pays off most for irreversible actions where a mistake dominates latency concerns.
- **Which learning signal.** Use imitation learning when high-quality traces exist (enterprise playbooks, curated code-repair sequences) and unsafe exploration is unacceptable; use preference optimization/RLHF to shape instruction-following and refusal; use RL only when the environment and action space are well-defined and interaction can be scaled safely.
- **What to measure.** Report a metric *vector*, not a single headline number: success rate, reward, time and step counts, tokens and cost, tool-call count and retries, tool-selection and argument accuracy, tool-execution success, recovery rate, valid-action rate, loop rate, robustness under perturbation, worst-case success, variance, policy-violation rate, and human-intervention rate.

### Evaluating agent systems (Bin Xu's evaluation framework)

Evaluation must be end-to-end and multi-dimensional because agent behavior is architecture- and system-dependent (planning depth, memory, verification loops, tool orchestration), and a single success metric is insufficient.

- **End-to-end task performance (primary):** task success/completion rate, environment score/reward, time-to-completion, and number of steps.
- **Efficiency and cost:** median and tail (p95) latency including tool runtime, token usage and dollar cost per successful completion, and tool-call count / retries / backtracking.
- **Tool-use correctness:** tool-selection accuracy, argument accuracy, tool-execution success, and recovery rate after failures.
- **Trajectory and planning quality (architecture-sensitive):** action validity rate, loop rate (repeating or oscillating states), and plan adherence/coherence against a reference plan. These "orchestration metrics" explain *why* an agent succeeds or fails and enable meaningful cross-design comparison.
- **Robustness and reliability:** success under perturbations (noisy instructions, missing info, changed UI layout, flaky tools), variance across seeds and prompt variants, and graceful degradation under token/tool budget caps.
- **Safety and compliance:** policy-violation rate (unsafe tool actions, data leakage, disallowed content) and human-intervention rate, tied to concrete threat models (prompt injection, untrusted tool outputs, permission escalation) and reported with trace artifacts.
- **Benchmarks and suites:** AgentBench (multi-environment interactive), WebArena (realistic web tasks, end-to-end success), ToolBench (tool selection, argument correctness, execution reliability), SWE-bench (end-to-end software-issue resolution via patches), and GAIA (short verifiable answers requiring tools).

### Open research directions (Bin Xu)

Verifiable and trustworthy tool execution (tool contracts as first-class objects with pre/postconditions and retained evidence); long-term memory and context management; planning and test-time-compute allocation; robust, reproducible evaluation under realistic variability; multi-agent coordination, role specialization, and governance; and unified conceptual frameworks and standardized agent interfaces (tool schemas, trace formats, eval harnesses) so progress is not dominated by prompt idiosyncrasies.

## Source attribution

- **Agent definition, the agent-transformer tuple and execution loop, risk-aware control, learning mechanisms (RL/IL/in-context/test-time compute), modules and infrastructure, multi-agent coordination and governance, and the full evaluation framework and benchmarks:** AI Agent Systems (Bin Xu, 2026).
- **Practical agent definition, prerequisite capabilities (planning, tool calling), the processing-loop mental model, MAS use cases, and the build-from-scratch framing of agent infrastructure:** Build a Multi-Agent System from Scratch (Fajardo, 2025).
- **Agent vs agentic workflow distinction and Anthropic's workflow patterns (prompt chaining, parallelization, routing, orchestrator-worker, evaluator-optimizer):** AI Agents with MCP (Stratis, 2025).
- **AI application patterns (raw, graph-based, orchestrated, composite agents), the "God agent" and "AI for AI's sake" anti-patterns, orchestration-framework landscape, and agent-vs-microservice comparison:** Building AI Agent Platforms (O'Mahony and Nonnenmacher, 2026).

## Recent developments (2024-2026)

- **Vendor agent SDKs proliferated and then converged.** OpenAI shipped its Agents SDK (March 2025), Google released ADK (April 2025), and Anthropic published the Claude Agent SDK. Despite different core models (LangGraph's directed graph, CrewAI's role-based crews, OpenAI's explicit handoffs, AutoGen/AG2's conversational GroupChat), all major frameworks converged on shared standards: MCP for tools, A2A for agent-to-agent calls, streaming, persistence, observability, and a ReAct-style loop (Composio / Langfuse comparisons, 2025-2026).
- **Microsoft merged its two frameworks.** Semantic Kernel and AutoGen were consolidated into the Microsoft Agent Framework (public preview October 2025, v1.0 April 2026); both predecessors entered maintenance mode. The unified SDK adds graph-based workflows and multi-provider support (Azure OpenAI, OpenAI, Anthropic, Bedrock) with A2A/MCP interoperability (Visual Studio Magazine, 2025-2026).
- **A quantitative science of agent scaling emerged, reversing "more agents is better."** Google Research / MIT's *Towards a Science of Scaling Agent Systems* (2025) showed that on genuinely agentic tasks (multi-step, partially observed, adaptive) multi-agent coordination often *degrades* performance: coordination benefits saturate once a single-agent baseline exceeds ~45% accuracy, tool-heavy tasks suffer most under fixed budgets, and independent agents amplify errors ~17x versus ~4x for centralized topologies. (See `agent-systems-research-frontier.md`.)
- **Agent memory consolidated into a recognized subfield.** 2025-2026 surveys formalized memory taxonomies (short/long-term, knowledge vs experience, structural/graph) and lifecycles (extract, store, retrieve, evolve). Anthropic shipped a file-based memory tool (public beta, 2025) and multi-agent research systems using subagents with isolated context windows returning condensed summaries became a documented pattern (Anthropic engineering / arXiv, 2025-2026).
- **Evaluation matured into agent observability.** OpenTelemetry GenAI Semantic Conventions (GenAI SIG, experimental as of early 2026) standardized trace attributes for LLM calls, agent steps, tool calls, and token/cost; Datadog launched AI Agent Monitoring (DASH 2025); LangSmith, Langfuse, and Arize gained traction. Frameworks (Pydantic AI, smolagents, Strands) now emit OTel traces natively, reinforcing this file's "evaluate the agent as a system" principle (OpenTelemetry / Greptime, 2025-2026).
- **Agent benchmarks advanced and drew scrutiny.** SWE-bench Verified (Claude Opus leading ~87%+ by early 2026), Terminal-Bench, OSWorld, GAIA, and tau-bench/tau2-bench (pass@k reliability, dual-control) became standard. Researchers flagged benchmark contamination and reward hacking, estimating 5-15 points of inflation on post-2023 models, underscoring this file's caution against single headline metrics (benchmarkingagents.com / arXiv, 2026).
- **Frontier base models reduced (but did not remove) coordination value.** GPT-5 (August 2025), Gemini 3 Pro (November 2025), Claude Opus 4.5 (November 2025), and open-weight DeepSeek-V3.2 raised single-agent capability sharply; the practical effect is fewer tasks where multi-agent overhead pays off, consistent with the capability-saturation finding (Vellum / vendor releases, 2025-2026).

Sources: [OpenAI Agents SDK vs LangGraph vs AutoGen vs CrewAI (Composio)](https://composio.dev/content/openai-agents-sdk-vs-langgraph-vs-autogen-vs-crewai), [Microsoft Agent Framework 1.0 (Visual Studio Magazine)](https://visualstudiomagazine.com/articles/2026/04/06/microsoft-ships-production-ready-agent-framework-1-0-for-net-and-python.aspx), [AI Agent Observability (OpenTelemetry)](https://opentelemetry.io/blog/2025/ai-agent-observability/), [Agent benchmarks 2026](https://benchmarkingagents.com/agent-benchmarks/), [Effective context engineering (Anthropic)](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents).
