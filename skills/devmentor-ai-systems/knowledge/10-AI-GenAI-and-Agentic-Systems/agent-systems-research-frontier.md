# Agent Systems Research Frontier (2025-2026)

**Domain:** 10 - AI, GenAI, and Agentic Systems
**Primary sources:** *Towards a Science of Scaling Agent Systems* (Kim, Gu, Park et al., Google Research / MIT, 2025); *Scaling up Multi-Turn Off-Policy RL and Multi-Agent Tree Search for LLM Step-Provers* (Xin, Zheng, Nie, Yuan, Xiao, ByteDance Seed, 2025-26); *Do We Always Need Query-Level Workflows? Rethinking Agentic Workflow Generation for Multi-Agent Systems* (Wang, Xu, Yuan, Shen, Cheng, 2026); *A Self-Correcting Multi-Agent LLM Framework for Language-Based Physics Simulation and Explanation* (Park, Moon, Ryu, npj Artificial Intelligence, 2026); *Graph-based Agent Memory: Taxonomy, Techniques, and Applications* (Yang, Zhou, Xiao et al., 2026); *Robust and Efficient Communication in Multi-Agent Reinforcement Learning* (Liu, Li, Wang et al., 2025); *Nonreciprocal Field Theory for Decision-Making in Multi-Agent Control Systems* (Lama, di Bernardo, Klapp, Nature Communications, 2025-26).

## Overview

This file distills the 2025-2026 research frontier on how to build, scale, train, coordinate, and reason about agent systems built on large language models (LLMs) and reinforcement learning (RL). The central practical question running through this material is **when do more agents and more coordination actually help, and when do they hurt?** The naive intuition that "more agents is all you need" turns out to be wrong on genuinely agentic tasks. Several papers converge on a more disciplined picture: coordination is a tax that only pays off under specific, measurable task conditions.

For software practitioners, this matters directly. Multi-agent systems (MAS) are now the default reach when a single agent struggles, but they introduce coordination overhead, error amplification, and token cost. The frontier work here gives quantitative principles, training recipes, memory architectures, and communication strategies for deciding *whether* to go multi-agent and *how* to build it when you do. The work spans scaling laws, off-policy RL for long-horizon reasoning, workflow-generation granularity, self-correcting agent loops, graph-structured memory, robust communication under realistic constraints, and continuum models of collective decision-making.

## Core concepts

**Agentic vs. non-agentic tasks (Kim et al.).** A task is agentic if it requires (i) sustained multi-step interaction with an external environment, (ii) iterative information gathering under partial observability, and (iii) adaptive strategy refinement based on environmental feedback. Static, single-shot, fully observed benchmarks (e.g., HumanEval) are *not* agentic. This distinction is load-bearing: MAS scaling results from non-agentic benchmarks (where "more agents" monotonically improves accuracy via voting) do not transfer to agentic tasks, where coordination overhead and error propagation dominate.

**Context integration vs. diversity trade-off (Kim et al.).** A single-agent system (SAS) maximizes context integration: one unified memory stream, constant-time access to full history. A MAS maximizes diversity through parallel exploration but pays an unavoidable **coordination tax** - global context must be compressed into lossy inter-agent messages, increasing synchronization overhead and causing agents to operate on progressively divergent world states.

**Coordination metrics (Kim et al.).** Four empirically measurable properties predict MAS performance: **efficiency** (useful work per token), **overhead** (coordination cost), **error amplification** (how mistakes propagate), and **redundancy** (duplicated effort). These feed a predictive model rather than overfitting to any one dataset.

**Training-time vs. inference-time scaling (Xin et al.).** Two distinct and interconnected scaling challenges for any long-horizon reasoning agent. *Training-time scaling* is sustaining model improvement via RL despite performance plateaus. *Inference-time scaling* is allocating compute at solve time (search, decomposition) to tackle unseen problems. A strong base model is necessary but not sufficient without an effective search strategy.

**Workflow granularity: task-level vs. query-level (Wang et al.).** Agentic workflow generation for MAS falls into two categories. *Task-level* generates one workflow intended to work across a whole task distribution (e.g., AFlow, GPTSwarm, AgentPrune). *Query-level* generates a fresh, tailored workflow for every individual input query. Query-level adapts better but is costly; the open question is whether per-query workflows are actually necessary.

**Agent memory taxonomy (Yang et al.).** Memory is the core module enabling knowledge accumulation, iterative reasoning, and self-evolution. It is taxonomized along three axes: **short-term vs. long-term**, **knowledge vs. experience**, and **non-structural vs. structural** (with graph as the structural implementation of choice). Knowledge memory is passive and static (facts, rules, references); experience memory is proactive and dynamic (trajectories, feedback, interaction logs).

**Memory lifecycle (Yang et al.).** Four stages: **extraction** (raw data into memory contents), **storage** (organizing data efficiently), **retrieval** (fetching relevant contents to support reasoning), and **evolution** (post-processing updates - consolidation, forgetting, refinement).

**Robust and efficient communication (Liu et al.).** Multi-agent RL (MARL) typically assumes instantaneous, reliable, unlimited-bandwidth communication. Real deployments violate all three. The frontier problem is co-designing communication, learning, and robustness against message perturbations, transmission delays, limited bandwidth, and adversarial attacks.

**Nonreciprocity and decision-making (Lama et al.).** When agents make decisions based on local observations to achieve a global control goal, they introduce *nonreciprocal* (asymmetric) and *non-pairwise* (many-body) couplings into the system dynamics. Traditional field theories assume reciprocal pairwise interactions and cannot capture this.

## Frameworks & models

**Predictive scaling model for agent systems (Kim et al.).** A controlled evaluation across 180 configurations: five canonical architectures (SAS plus four MAS variants - Independent, Centralized, Decentralized, Hybrid), three LLM families, four agentic benchmarks (Finance-Agent, BrowseComp-Plus, PlanCraft, Workbench). Tools, prompt structures, and token budgets are standardized to isolate architecture from implementation confounds. The model uses the four coordination metrics to predict performance, achieving cross-validated R-squared of 0.513 on unseen domains (0.89 on held-out data in the conclusion's framing) and correctly predicting the optimal coordination strategy for 87% of held-out configurations. Three dominant effects:
- **Tool-coordination trade-off:** under fixed compute budgets, tool-heavy tasks suffer disproportionately from multi-agent overhead (strongest predictor, beta = -0.330).
- **Capability saturation:** coordination yields diminishing or negative returns (beta = -0.408, p < 0.001) once the single-agent baseline exceeds roughly 45% accuracy.
- **Topology-dependent error amplification:** independent agents amplify errors 17.2x through unchecked propagation; centralized coordination contains this to 4.4x.

**The four MAS topologies (Kim et al.).**
- *Independent:* agents work in parallel with no coordination - maximum diversity, maximum error amplification.
- *Centralized:* an orchestrator passes findings between itself and workers - best error containment, best on parallelizable tasks.
- *Decentralized:* agents communicate peer-to-peer - excels on dynamic tasks like web navigation.
- *Hybrid:* mixes centralized and decentralized structure - generally strong scaling efficiency.

**BFS-Prover-V2 dual-scaling system (Xin et al.).** A step-level theorem prover for Lean4 addressing both scaling challenges:
- *Training:* a distillation-free multi-stage **expert-iteration** pipeline (a form of off-policy RL inspired by AlphaZero). Best-first tree search generates roughly 10^7 searches per round, producing a massive synthetic dataset. Two innovations sustain learning past plateaus: (1) **adaptive perplexity-based tactic-level data filtering** that creates an automatic curriculum, and (2) **periodic retraining** acting as a "soft reset" to escape local optima in parameter space.
- *Inference:* a **planner-enhanced multi-agent tree search**. A general reasoning model (the Planner) iteratively decomposes complex theorems into simpler subgoals; a team of parallel prover agents tackles subgoals while sharing a common proof cache, dramatically shrinking the search space.
- *Results:* 95.08% on miniF2F and 41.4% on ProofNet. The reward is sparse: +1 for state-action pairs on a successful proof path, 0 otherwise.

**SCALE - low-cost task-level workflow generation (Wang et al.).** SCALE = "Self prediction of the optimizer with few-shot CALibration for Evaluation." Built on two empirical findings: (1) query-level workflow generation is often unnecessary because a small set of top-K best task-level workflows together already covers equivalent or more queries; (2) exhaustive execution-based task-level evaluation is extremely token-costly and frequently unreliable. Inspired by self-evolution and generative reward modeling, SCALE predicts the optimizer's evaluation outcome with few-shot calibration instead of running full validation execution. It maintains competitive performance (average degradation of just 0.61%) while cutting overall token usage by up to 83%.

**MCP-SIM - Memory-Coordinated Physics-Aware Simulation (Park et al.).** A self-correcting multi-agent framework that turns vague, incomplete, or multilingual natural-language prompts into validated physics simulations plus explanatory reports. Six specialized agents (Input Clarifier, Code Builder, Simulation Executor, Error Diagnosis, Input Rewriter, Mechanical Insight) are governed by a central **Memory-Centric Orchestrator** over persistent shared memory. The control loop is **Plan -> Act -> Reflect -> Revise**, emulating expert reasoning rather than one-shot code generation. Demonstrated across twelve tasks (elasticity, heat transfer, multiphysics, fracture mechanics), solving all benchmark cases and improving convergence efficiency over GPT-based and human-in-the-loop baselines.

**Graph-based memory framework (Yang et al.).** Graph stands out for agent memory because it natively models relational dependencies, organizes hierarchical information, and supports efficient retrieval. Graph types used include knowledge graphs (KGs), temporal graphs, and others. The survey maps the memory lifecycle (extraction, storage, retrieval, evolution) onto graph implementations and catalogs open-source libraries and benchmarks for self-evolving memory.

**Robustness-and-bandwidth co-design (Liu et al.).** MARL communication is built on the Dec-POMDP foundation. The survey organizes solutions into (1) robust MARL with unreliable inputs and communication (handling observation noise, message perturbations, malicious tampering, structural disruptions, adversarial attacks via robust equilibria and robust actor-critic methods) and (2) communication efficiency (message compression and sparsification, decision-centric scheduling, efficient information integration to cut bandwidth while preserving coordination). Applications: cooperative autonomous driving, distributed SLAM, federated learning.

**Nonreciprocal field theory for shepherding (Lama et al.).** Uses the swarm-robotics shepherding problem (herders confine targets to a goal region) as a paradigm for multi-agent control with decision-making. By introducing continuous approximations of two decision-making elements - **target selection** and **trajectory planning** - it derives field equations (PDEs) capturing the distributed control problem. Different decision strategies emerge at the continuum level (from average attraction to highly selective choice; from undirected to goal-oriented motion), driving phase transitions between homogeneous and confined configurations. Provides a general method for embedding decision-making into continuum theories of collective behavior, applicable to robotic swarms, traffic, and crowd management.

## Patterns

**Coordination-tax gating** - Intent: decide whether to use a MAS at all before building one. When to use: any time you are tempted to add agents. Apply Kim et al.'s gates - if the single-agent baseline already exceeds roughly 45% accuracy, or the task is tool-heavy under a fixed budget, or the task is sequential rather than parallelizable, prefer a single agent. Trade-offs: requires measuring or estimating baseline accuracy and task properties first; the payoff is avoiding 39-70% degradation seen when MAS is misapplied to sequential reasoning.

**Topology-to-task matching** - Intent: pick the coordination structure that fits the task's information-flow needs. When to use: once you have decided a MAS is warranted. Use centralized for parallelizable, decomposable tasks (best error containment, +80.9% on financial reasoning); decentralized for dynamic, exploratory tasks like web navigation (+9.2% vs. +0.2% for centralized there); avoid independent topology unless diversity strictly dominates and you have downstream error correction. Trade-offs: centralized adds an orchestrator bottleneck; decentralized risks divergent world states.

**Planner-decompose-and-cache** - Intent: scale inference-time reasoning on long-horizon search problems. When to use: combinatorially large search spaces (theorem proving, planning, complex reasoning). A high-level planner decomposes the goal into subgoals; parallel workers solve subgoals against a shared cache that deduplicates effort. Trade-offs: requires a capable planner model and a meaningful subgoal-cache abstraction; brittle if decomposition is poor.

**Expert iteration with soft resets** - Intent: sustain RL improvement past performance plateaus. When to use: long-running off-policy RL on reasoning agents that stagnate. Combine adaptive (perplexity-based) data filtering for automatic curriculum with periodic retraining that resets to escape local optima. Trade-offs: periodic retraining is compute-intensive; data filtering thresholds need tuning.

**Plan-Act-Reflect-Revise self-correction loop** - Intent: handle underspecified or ambiguous inputs without one-shot failure. When to use: tasks where prompts are vague, incomplete, or multilingual and verification is available (code execution, simulation). Specialized agents over shared memory clarify input, act, diagnose errors, and rewrite. Trade-offs: more LLM calls and latency than one-shot generation; needs a reliable verifier (compiler, solver) to ground the reflect step.

**Top-K task-level workflow reuse** - Intent: avoid expensive per-query workflow generation. When to use: MAS workflow generation where many queries are simple or similar. Maintain a small library of top-K task-level workflows that together cover the query distribution; predict the best one with few-shot calibration instead of full execution. Trade-offs: minor accuracy loss (~0.6%) for major token savings (~83%); degrades when queries are genuinely heterogeneous and need bespoke workflows.

**Graph-structured experience memory** - Intent: give agents long-term reasoning beyond the context window and the ability to learn from past successes and failures. When to use: long-horizon, iterative tasks where agents otherwise repeat the same mistakes. Store experience as a graph supporting relational and hierarchical retrieval; run the extraction-storage-retrieval-evolution lifecycle. Trade-offs: graph construction and maintenance overhead; retrieval quality depends on schema design.

**Communication-learning-robustness co-design** - Intent: keep MARL coordination stable under realistic communication constraints. When to use: real deployments (autonomous driving, distributed SLAM, federated learning) with noise, delay, bandwidth limits, or adversaries. Jointly design message compression/sparsification, decision-centric scheduling, and adversarial robustness rather than treating communication as a perfect channel. Trade-offs: robustness and efficiency can conflict; co-design is harder than optimizing one axis.

## Principles & best practices

- **Evaluate agentic claims on agentic tasks.** MAS results from static benchmarks mislead. Coordination benefits are task-contingent (Kim et al.).
- **Coordination is a tax, not a free lunch.** It pays off only on parallelizable, decomposable tasks below the capability-saturation threshold (Kim et al.).
- **More capable base models reduce the value of coordination.** Past ~45% single-agent accuracy, adding agents yields diminishing or negative returns (Kim et al.).
- **Contain errors structurally.** Centralized topologies cut error amplification roughly 4x relative to independent agents (Kim et al.).
- **Decompose to shrink search.** Hierarchical planner-worker decomposition with shared caching makes intractable search tractable (Xin et al.).
- **Sparse, verifiable rewards plus expert iteration can drive long-horizon RL** when paired with curriculum filtering and periodic resets (Xin et al.).
- **Do not pay query-level costs when task-level reuse suffices.** Measure coverage before committing to per-query workflows (Wang et al.).
- **Prefer iterative refine-and-verify over one-shot generation** for underspecified inputs, when a verifier exists (Park et al.).
- **Memory is a first-class architectural concern.** Separate knowledge (static) from experience (dynamic); choose structure (graph) to enable relational retrieval and evolution (Yang et al.).
- **Never assume a perfect communication channel** in distributed multi-agent deployments (Liu et al.).
- **Local decision rules induce global, nonreciprocal dynamics.** Designing distributed control means reasoning about emergent collective behavior, not just individual policies (Lama et al.).

## Anti-patterns & pitfalls

- **"More agents is all you need."** Empirically false on agentic tasks; every MAS variant tested degraded sequential-reasoning performance by 39-70% (Kim et al.).
- **Applying MAS to sequential reasoning.** Errors cascade through execution chains rather than being corrected by voting (Kim et al.).
- **Heavy tool use plus heavy coordination under a fixed budget.** The two compete for the same tokens; tool-heavy tasks suffer most from multi-agent overhead (Kim et al.).
- **Unchecked independent agents.** 17.2x error amplification through unchecked propagation (Kim et al.).
- **One-shot code/simulation generation from vague prompts.** Fails on underspecified problems; lacks iterative refinement (Park et al.).
- **Exhaustive execution-based workflow evaluation.** Extremely token-costly (10^6-10^8 tokens) and frequently unreliable (Wang et al.).
- **Linear, unstructured, or simple key-value memory.** Fails to capture relational/hierarchical structure needed for long-horizon reasoning; leads to repeated errors and performance saturation (Yang et al.).
- **Assuming reliable, unlimited-bandwidth, instantaneous communication.** Idealized assumptions are violated in the real world and destabilize MARL (Liu et al.).
- **Reciprocal pairwise interaction models for goal-directed swarms.** They miss the nonreciprocity and many-body effects that decision-making introduces (Lama et al.).

## Decision guidance

An agent reasoning about whether and how to build a multi-agent system should:

1. **Classify the task.** Is it agentic (environmental interaction, partial observability, adaptive refinement) or static? MAS heuristics only transfer to agentic tasks.
2. **Measure the single-agent baseline.** If it exceeds ~45%, lean single-agent; coordination likely yields negative returns.
3. **Characterize task structure.** Parallelizable/decomposable -> centralized MAS is promising. Dynamic/exploratory -> decentralized. Sequential -> stay single-agent.
4. **Check the budget-vs-tools profile.** Tool-heavy under a fixed token budget -> avoid coordination overhead.
5. **If long-horizon search:** add a planner that decomposes goals and a shared cache for workers (Xin et al.).
6. **If inputs are underspecified and a verifier exists:** use a Plan-Act-Reflect-Revise loop over shared memory (Park et al.).
7. **For workflow generation:** start with top-K task-level workflows and few-shot calibrated selection; escalate to query-level only where coverage is provably insufficient (Wang et al.).
8. **For long-horizon memory:** use graph-structured memory, separating knowledge from experience and running the full extraction-storage-retrieval-evolution lifecycle (Yang et al.).
9. **For real distributed deployment:** co-design communication efficiency and adversarial robustness from the start (Liu et al.).

## Source attribution

- Scaling laws, coordination metrics, topology-to-task matching, capability saturation, error-amplification figures, agentic-vs-non-agentic distinction: Kim, Gu, Park et al., *Towards a Science of Scaling Agent Systems* (Google Research / Google DeepMind / MIT, 2025).
- Training-time vs. inference-time scaling, expert iteration, perplexity-based filtering, periodic retraining, planner-enhanced multi-agent tree search, shared proof cache, BFS-Prover-V2 results: Xin, Zheng, Nie, Yuan, Xiao, *Scaling up Multi-Turn Off-Policy RL and Multi-Agent Tree Search for LLM Step-Provers* (ByteDance Seed / CMU / Peking University, 2025-26).
- Task-level vs. query-level workflows, top-K coverage finding, SCALE framework, token-cost critique: Wang, Xu, Yuan, Shen, Cheng, *Do We Always Need Query-Level Workflows?* (Institute of Computing Technology, CAS, 2026).
- MCP-SIM, Plan-Act-Reflect-Revise loop, Memory-Centric Orchestrator, six-agent self-correcting architecture: Park, Moon, Ryu, *A Self-Correcting Multi-Agent LLM Framework for Language-Based Physics Simulation and Explanation* (KAIST, npj Artificial Intelligence, 2026).
- Memory taxonomy (short/long-term, knowledge/experience, structural/non-structural), memory lifecycle (extraction/storage/retrieval/evolution), graph-based memory rationale: Yang, Zhou, Xiao et al., *Graph-based Agent Memory: Taxonomy, Techniques, and Applications* (Hong Kong PolyU et al., 2026).
- Robust/efficient MARL communication, Dec-POMDP framing, message perturbation/delay/bandwidth/adversarial taxonomy, co-design principle: Liu, Li, Wang et al., *Robust and Efficient Communication in Multi-Agent Reinforcement Learning* (2025).
- Nonreciprocity and many-body couplings from decision-making, shepherding field theory, target selection and trajectory planning, phase transitions: Lama, di Bernardo, Klapp, *Nonreciprocal Field Theory for Decision-Making in Multi-Agent Control Systems* (Nature Communications, 2025-26).

## Recent developments (2024-2026)

This entire file documents 2025-2026 frontier work and should be read as forward-looking knowledge:

- **A quantitative science of agent scaling is emerging.** The field is moving from heuristics ("more agents is better") to predictive models grounded in measurable task and coordination properties (Kim et al., 2025). The headline reversal: on genuinely agentic tasks, multi-agent coordination often *degrades* performance, and the benefit is strongly task-contingent.
- **AlphaZero-style off-policy RL is being adapted to LLM reasoning agents**, with specific recipes (adaptive curriculum filtering, periodic soft resets) to overcome the RL performance plateaus that previously curtailed long-horizon training (Xin et al., 2025-26).
- **Workflow-generation cost is under scrutiny.** 2026 work shows per-query workflow generation is frequently unnecessary and that calibrated task-level reuse cuts token cost by up to 83% with negligible accuracy loss (Wang et al., 2026).
- **Self-correcting, memory-coordinated agent loops** are reaching scientific-computing applications, turning vague natural-language prompts into validated, explained simulations (Park et al., 2026).
- **Graph-structured, self-evolving memory** is consolidating into a recognized subfield with its own taxonomy, lifecycle model, libraries, and benchmarks (Yang et al., 2026).
- **Realistic communication constraints** (noise, delay, bandwidth, adversaries) are now treated as first-class design concerns in MARL rather than idealized away (Liu et al., 2025).
- **Continuum / field-theoretic models of multi-agent decision-making** are bridging statistical physics and control theory, formalizing how local decision rules produce nonreciprocal, many-body collective dynamics (Lama et al., 2025-26).

Broader ecosystem context that this frontier work sits within (as of June 2026):

- **Protocol standardization closed underneath the research.** MCP moved to the Linux Foundation / Agentic AI Foundation (December 2025) and A2A to the Linux Foundation (June 2025), so the coordination and communication primitives these papers study now have neutral, widely adopted standards and an official MCP registry (Linux Foundation, 2025).
- **Frontier base models advanced fast, amplifying the capability-saturation finding.** GPT-5 (August 2025), Gemini 3 Pro and Claude Opus 4.5 (November 2025), and open-weight DeepSeek-V3.2 with sparse attention (December 2025) raised single-agent baselines sharply, which per Kim et al. shrinks the set of tasks where multi-agent coordination pays off (Vellum / vendor releases, 2025-2026).
- **Agent benchmarking and its limits drew scrutiny.** SWE-bench Verified, Terminal-Bench, OSWorld, GAIA, and tau2-bench (pass@k reliability, dual-control) became standard, but 2026 analyses flagged contamination and reward hacking, estimating 5-15 points of inflation on post-2023 models, reinforcing this file's emphasis on evaluating agentic claims rigorously and on agentic tasks (benchmarkingagents.com / arXiv, 2026).
- **Agent memory consolidated into a recognized subfield with production tooling.** Beyond the graph-memory survey here, Anthropic shipped a file-based memory tool (public beta, 2025) and context engineering became a named discipline (Anthropic September 2025; LangChain July 2025), moving long-term-memory research toward deployed systems (Anthropic / LangChain, 2025).

Sources: [One Year of MCP](https://blog.modelcontextprotocol.io/posts/2025-11-25-first-mcp-anniversary/), [Linux Foundation A2A launch](https://www.linuxfoundation.org/press/linux-foundation-launches-the-agent2agent-protocol-project-to-enable-secure-intelligent-communication-between-ai-agents), [Agent benchmarks 2026](https://benchmarkingagents.com/agent-benchmarks/), [Effective context engineering (Anthropic)](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents).
