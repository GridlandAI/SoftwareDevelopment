# Distributed and Real-Time Computing Foundations

**Domain:** 07 - Distributed Systems, Scalability, Resilience
**Primary sources:** *Hard Real-Time Computing Systems: Predictable Scheduling Algorithms and Applications* (Giorgio C. Buttazzo, 4th ed., Springer, 2024); *Parallel and Distributed Computing, Applications and Technologies* (PDCAT 2023 proceedings; Park, Takizawa, Shen, Park, eds., Springer Lecture Notes in Electrical Engineering, 2023)

## Overview

Where the data-intensive-systems file covers scaling stateful data, this file covers two adjacent foundations: how computation itself is parallelized and distributed, and how systems with timing requirements are made *predictable*. Parallel and distributed computing supplies the models and mechanisms (parallelism, communication, scheduling, coordination, fault tolerance) that let many processors cooperate on one workload. Hard real-time computing supplies a rigorous theory of scheduling under deadlines: the value of a result depends not only on its correctness but on *when* it is produced, and in a hard real-time system a missed deadline is a system failure, not a quality-of-service blip.

Both fields matter to software architects building scalable and resilient systems. The parallel/distributed material informs how to decompose and coordinate work across cores, GPUs, and nodes; the real-time material gives the only mathematically grounded answers to "will this set of tasks always meet its deadlines on this processor?" and "how do I share resources without unbounded blocking?" The two converge in modern edge, robotics, control, and high-throughput systems where both throughput and timeliness must be guaranteed.

## Core concepts

### Parallel and distributed computing

- **Parallelism vs distribution** - parallel computing exploits multiple processing elements (cores, GPUs, accelerators) usually within one machine with shared or tightly-coupled memory; distributed computing coordinates autonomous nodes communicating over a network with no shared memory and partial failure. The same workload may be both (e.g., a multi-GPU cluster).
- **Decomposition** - splitting a problem into tasks: *data parallelism* (the same operation over partitioned data, the basis of GPU and SIMD computing and of CFD solvers) and *task/functional parallelism* (different operations concurrently). Effective speedup is bounded by the serial fraction (Amdahl's law) and by communication and synchronization overhead.
- **Communication models** - *message passing* (explicit sends/receives, e.g., MPI; the standard for HPC clusters) and *shared memory* (threads coordinating through common state with locks/atomics). *Collective operations* such as Allreduce combine and redistribute data across all ranks; non-blocking variants overlap communication with computation and can double as liveness/health checks in dynamic simulations.
- **Coordination and consensus** - distributed nodes must agree despite faults. Atomic broadcast protocols (e.g., ZooKeeper's Zab) provide ordered, fault-tolerant message delivery as the basis of coordination services; blockchain-style systems reach agreement through majority/reputation-based consensus. Formal methods (process algebra such as CSP, model checking) are used to verify that such protocols actually preserve consistency and fault tolerance.
- **Scheduling of parallel/distributed work** - mapping tasks to resources to minimize makespan or meet QoS: *list-based workflow scheduling* orders ready tasks by priority onto heterogeneous resources; modern work applies deep reinforcement learning to learn scheduling policies, and *coflow scheduling* optimizes groups of related network flows (e.g., in optical circuit-switched datacenter networks).
- **Edge/cloud and offloading** - task offloading and content caching place computation and data near demand (e.g., Internet-of-Vehicles in cloud-edge environments, UAV traffic offloading) to trade local compute against network latency and energy.
- **Federated and privacy-preserving computation** - *federated learning* trains a shared model across distributed data holders without centralizing raw data (e.g., across hospitals for skin-cancer classification); related schemes use encryption and blockchain for privacy-preserving retrieval and reliable IoT data exchange.

### Hard real-time systems

- **Real-time correctness** - a result must be both logically correct and produced within its timing constraint. *Hard* deadlines must always be met (a miss is a catastrophic failure); *soft/firm* deadlines tolerate occasional misses with degraded value.
- **Task model** - a real-time workload is a set of tasks. A *periodic* task is an infinite sequence of identical jobs (instances) released every period T, each with computation time C and a relative deadline D. An *aperiodic* task arrives irregularly; a *sporadic* task is aperiodic with a known minimum inter-arrival time. Key parameters: arrival/release time, worst-case execution time (WCET) C, period T, deadline D, and utilization U = C/T.
- **Predictability** - the defining goal: the system's timing behavior must be analyzable in advance so that schedulability can be *guaranteed*, not merely observed. This requires bounded WCET, bounded interference, and bounded blocking on shared resources.
- **Schedulability analysis** - a test (sufficient, or necessary-and-sufficient) that decides offline whether all deadlines will always be met under a given scheduling algorithm.
- **Resource sharing hazards** - *priority inversion* (a high-priority task blocked by a lower-priority one holding a needed resource), which without a protocol can be unbounded; *chained blocking*; and *deadlock*.

## Frameworks & models

### Rate Monotonic (RM) scheduling

Fixed-priority preemptive scheduling where priority is assigned by rate: the shorter the period, the higher the priority. RM is the *optimal fixed-priority* algorithm for periodic tasks with deadlines equal to periods.

- **Liu and Layland utilization bound** - a set of n periodic tasks is schedulable under RM if total utilization U = Σ(Cᵢ/Tᵢ) ≤ n(2^(1/n) − 1). This least-upper-bound decreases with n (1.000 for n=1, 0.828 for n=2, 0.780 for n=3 …) and converges to ln 2 ≈ 0.69 as n → ∞. It is *sufficient but not necessary*: task sets above the bound may still be schedulable.
- **Hyperbolic bound** - a tighter sufficient test of the same complexity: schedulable if Π(Uᵢ + 1) ≤ 2. Accepts some sets the Liu-Layland bound rejects.
- **Response Time Analysis (RTA)** - an exact (necessary-and-sufficient) test: compute each task's worst-case response time via the fixed-point iteration Rᵢ = Cᵢ + Σ_{j∈hp(i)} ⌈Rᵢ/Tⱼ⌉Cⱼ over higher-priority tasks; the set is schedulable iff every Rᵢ ≤ Dᵢ. Handles deadlines less than periods, unlike the simple utilization bound.

### Earliest Deadline First (EDF) scheduling

Dynamic-priority preemptive scheduling where, at every instant, the ready job with the nearest absolute deadline runs.

- **Optimality** - EDF is optimal among all (static or dynamic) preemptive scheduling algorithms on a single processor: if any algorithm can schedule the set, EDF can.
- **Utilization test** - for periodic tasks with deadlines equal to periods, EDF schedulability is simply U ≤ 1 (necessary and sufficient). EDF therefore achieves full processor utilization, versus RM's ~0.69 worst case.
- **Deadlines less than periods** - when D < T, use the *processor demand criterion* (demand-bound analysis) rather than the plain utilization test.
- **RM vs EDF trade-off** - RM is simpler to implement (static priorities, easy in commercial RTOSes), more predictable under overload (lower-rate tasks miss first), but wastes capacity. EDF uses the CPU fully and handles aperiodic load gracefully, but has higher run-time overhead (deadline tracking) and degrades less predictably under transient overload (a domino effect of misses).

### Aperiodic and sporadic servers

Mechanisms to service irregular work while preserving the guarantees of the periodic set: Background service, Polling Server, Deferrable Server, Priority Exchange, Sporadic Server (fixed-priority), and their dynamic-priority EDF counterparts (Dynamic Sporadic Server, Total Bandwidth Server, Earliest Deadline Late Server). Each reserves a bounded "budget" of execution so aperiodic jobs get responsive service without endangering hard periodic deadlines.

### Resource access protocols (bounding blocking)

- **Priority Inheritance Protocol (PIP)** - a task holding a resource temporarily inherits the highest priority of any task it blocks, bounding the blocking time and preventing unbounded priority inversion. Does not prevent deadlock or chained blocking.
- **Priority Ceiling Protocol (PCP)** - each resource has a priority ceiling equal to the highest priority of any task that may use it; a task can lock a resource only if its priority exceeds the ceilings of all currently locked resources. Bounds blocking to a single critical section and prevents deadlock and chained blocking, at the cost of more complex run-time bookkeeping.
- **Stack Resource Policy (SRP)** - a unified policy supporting both fixed and dynamic priorities and multi-unit resources; a task is blocked at release (not at access) until its priority exceeds the system ceiling, which permits stack sharing and yields the same strong properties as PCP.

## Patterns

(Also recorded in PATTERN-CATALOG.md.)

- **Rate Monotonic priority assignment** - Intent: give shorter-period periodic tasks higher fixed priority for optimal fixed-priority scheduling. When to use: periodic hard real-time tasks on an RTOS with static priorities. Trade-offs: simple and analyzable; wastes up to ~31% of CPU at the worst-case utilization bound.
- **EDF dynamic scheduling** - Intent: always run the job with the nearest absolute deadline. When to use: maximizing utilization (U up to 1) and servicing mixed periodic/aperiodic load. Trade-offs: full utilization and optimality vs higher run-time overhead and less graceful overload behavior.
- **Response Time Analysis** - Intent: prove schedulability exactly by computing each task's worst-case response time. When to use: fixed-priority systems, especially when deadlines differ from periods. Trade-offs: exact and general; iterative computation per task.
- **Aperiodic/sporadic server (bandwidth reservation)** - Intent: bound the interference of irregular jobs by giving them a replenishing execution budget. When to use: mixing event-driven aperiodic work with guaranteed periodic tasks. Trade-offs: responsive aperiodic service while preserving hard guarantees; adds server-management complexity.
- **Priority Ceiling / Stack Resource Policy** - Intent: bound priority-inversion blocking to one critical section and prevent deadlock/chained blocking. When to use: real-time tasks sharing mutually-exclusive resources. Trade-offs: strong guarantees vs ceiling bookkeeping; PIP is simpler but weaker.
- **Non-blocking collective communication (overlap)** - Intent: overlap a collective such as Allreduce with computation, doubling as a liveness/health check. When to use: bulk-synchronous parallel and dynamic HPC simulations. Trade-offs: hides communication latency and detects stragglers/failures; more complex progress management.
- **List-based workflow scheduling** - Intent: order ready tasks by computed priority and greedily map them onto heterogeneous resources to minimize makespan. When to use: DAG workflows on clusters/grids. Trade-offs: fast, scalable heuristics; learned (RL) policies can beat them but cost training and lose explainability.
- **Atomic broadcast coordination (Zab-style)** - Intent: deliver ordered, fault-tolerant updates so all replicas agree, underpinning a coordination service. When to use: leader-based coordination, configuration, locking in distributed systems. Trade-offs: strong consistency and fault tolerance vs leader-election cost and reduced availability under partitions; correctness warrants formal verification.

## Principles & best practices

- Design for *predictability* before performance in real-time systems: bound WCET, bound interference, and bound blocking, then prove schedulability offline rather than testing empirically.
- Priority inversion must be eliminated, not merely reduced; always pair shared resources with a resource-access protocol (PIP at minimum, PCP/SRP for deadlock freedom).
- Choose the scheduling discipline to match the goal: RM/RTA when implementation simplicity and overload predictability matter; EDF when you need to use the whole processor or service heavy aperiodic load.
- In parallel computing, minimize and overlap communication; the serial fraction and synchronization cost, not the number of cores, bound real speedup.
- Prefer collective and non-blocking communication primitives over hand-rolled point-to-point for scalability and built-in fault detection.
- Push computation and data toward where they are consumed (edge/offloading) when network latency or energy dominates, and keep data local when privacy or bandwidth forbids centralization (federated learning).
- Verify coordination/consensus protocols with formal methods; "looks correct" is not a proof of consistency under faults.

## Anti-patterns & pitfalls

- **Unbounded priority inversion** - sharing resources without a protocol; the classic cause of real-time failures (famously the Mars Pathfinder reset).
- **Trusting the simple utilization bound when D < T** - the Liu-Layland and EDF U-tests assume deadlines equal periods; with shorter deadlines you must use RTA or the processor demand criterion.
- **Running near 100% utilization under RM** - sets above ~0.69 may still be schedulable, but assuming so without RTA risks missed deadlines; conversely rejecting them on the bound alone wastes capacity.
- **Ignoring WCET pessimism and overload behavior** - EDF can suffer a domino effect of cascading misses under transient overload; plan overload handling and admission control.
- **Over-synchronizing parallel code** - excess locking and global barriers serialize execution and erase speedup; the serial fraction dominates (Amdahl).
- **Treating distributed consensus as a library call** - partial failure, network partitions, and message reordering break naive agreement; use proven protocols and verify them.
- **Centralizing data that cannot legally or practically be centralized** - bandwidth, latency, and privacy constraints often make federated/edge approaches mandatory, not optional.

## Decision guidance

- **Real-time scheduling choice:** static priorities on a commercial RTOS, need predictable overload degradation → RM with Response Time Analysis. Need maximum CPU utilization or strong aperiodic responsiveness, can afford dynamic-priority overhead → EDF, with a bandwidth server for aperiodic jobs.
- **Schedulability test choice:** D = T and a quick yes/no → Liu-Layland or hyperbolic bound (RM) / U ≤ 1 (EDF). D ≠ T or you need an exact answer → RTA (fixed-priority) or processor demand criterion (EDF).
- **Resource sharing:** any mutual exclusion among real-time tasks → at least PIP; if deadlock or chained blocking is possible, use PCP or SRP; SRP additionally enables stack sharing and supports EDF.
- **Parallelization model:** tightly-coupled numeric workloads with regular data structure (CFD, deep learning) → data parallelism on GPUs/SIMD with collective communication. Irregular task graphs → task parallelism with list/DAG scheduling. Cross-machine scale → message passing (MPI) with non-blocking collectives.
- **Placement:** latency- or energy-critical and locally serviceable → edge/offload. Privacy- or bandwidth-constrained data → federated/distributed training. Strong global agreement required → a verified consensus/atomic-broadcast service, accepting its availability cost under partitions.

## Source attribution

- Real-time correctness, task model (periodic/aperiodic/sporadic, C/T/D/U), predictability, priority inversion: *Buttazzo* Ch. 1–2.
- Aperiodic task scheduling and servers: *Buttazzo* Ch. 3 and the aperiodic-service chapters.
- RM, Liu-Layland utilization bound (ln 2 ≈ 0.69) and its derivation, hyperbolic bound, EDF optimality and U ≤ 1 test, EDF with D < T, RM vs EDF comparison, Response Time Analysis: *Buttazzo* Ch. 4 (Periodic Task Scheduling).
- Aperiodic servers (Polling, Deferrable, Sporadic, Total Bandwidth, EDL): *Buttazzo* Ch. 5–6.
- Priority Inheritance, Priority Ceiling, Stack Resource Policy and their blocking/ deadlock properties: *Buttazzo* Ch. 7 (Resource Access Protocols).
- Parallelism models, MPI non-blocking Allreduce for health checks, multi-GPU CFD data-centric parallelism, list-based and RL workflow scheduling, coflow scheduling, federated learning, edge/cloud task offloading, blockchain/consensus and Zab/CSP formal verification: *PDCAT 2023 proceedings* (individual contributed papers; Park, Takizawa, Shen, Park, eds.).

## Recent developments (2024–2026)
- **Mixed-criticality scheduling is the dominant real-time research frontier, now at the edge.** 2025 work targets mixed-criticality DAG applications in vehicular/edge computing, transforming them into uniprocessor-equivalent problems and combining evolutionary optimization with game-theoretic server assignment, extending the classic RM/EDF single-processor theory in this file toward heterogeneous multicore and distributed edge platforms. (Wiley, "Mixed-Criticality Scheduling Toward Real-Time Applications in a Vehicular Edge Computing System", 2025, https://onlinelibrary.wiley.com/doi/abs/10.1002/cpe.70226; IntechOpen survey, 2025, https://www.intechopen.com/chapters/1214343)
- **Reinforcement learning enters real-time scheduling.** Recent work applies RL to improve mixed-criticality scheduling decisions, paralleling the learned-scheduling trend already noted for parallel/distributed workflow scheduling; the trade-off remains explainability and guarantee-ability versus learned performance gains. (arXiv, "Improving Mixed-Criticality Scheduling with Reinforcement Learning", 2025, https://arxiv.org/pdf/2504.03994)
- **Multicore shared-resource protocols continue to evolve.** New resource-access protocols (e.g., Imprecise Mixed-Criticality with MSRP) address blocking on shared resources across cores at different criticality levels, advancing the PIP/PCP/SRP lineage into the multiprocessor setting. (IntechOpen task-allocation survey, 2025, https://www.intechopen.com/chapters/1214343)
- **Edge AI fuses real-time and energy constraints.** A 2025 thread targets latency-aware, energy-efficient multicore task scheduling for real-time edge AI, where predictable deadlines and accelerator energy budgets must hold simultaneously, the convergence of the two foundations this file covers. (h-tsp.com IJAASR, "Energy Efficient Multi Core Task Scheduling for Real Time Edge AI Systems", 2025, https://h-tsp.com/index.php/ijaasr/article/view/260)
- **The foundational theory is stable.** Rate Monotonic with Liu-Layland/hyperbolic bounds and RTA, EDF optimality and U<=1, aperiodic servers, and the PIP/PCP/SRP resource protocols (Buttazzo 4th ed., 2024) remain the rigorous core; 2025-2026 work extends them to multicore, mixed-criticality, edge, and learned scheduling rather than replacing them.
