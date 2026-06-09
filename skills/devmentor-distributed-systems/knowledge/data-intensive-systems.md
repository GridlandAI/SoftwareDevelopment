# Data-Intensive Systems

**Domain:** 07 - Distributed Systems, Scalability, Resilience
**Primary sources:** *Designing Data-Intensive Applications* (Martin Kleppmann, O'Reilly; early-release text dated 2014, building toward the 2017 first edition)

## Overview

A data-intensive application is one whose main challenge is the volume, complexity, or rate of change of data, rather than raw CPU. Most modern systems are assembled from standard building blocks: databases (store data so it can be found again), caches (remember an expensive result), search indexes (let users filter and search), stream processors (send messages between processes asynchronously), and batch processors (periodically crunch large accumulated data). The architect's job is to combine these tools into an application-specific data system whose external API hides the internal complexity, and to do so while honoring three concerns that matter in almost every system: reliability, scalability, and maintainability.

This file distills Kleppmann's framework for reasoning about such systems: how to think about the three core concerns, the major data models and query languages, how storage engines actually work, how data is encoded and evolved, and the hard problems that arise once data is distributed across many machines (replication, partitioning, transactions, faults, consistency, and consensus), plus the two main dataflow styles (batch and stream processing). It is the anchor reference for the domain.

## Core concepts

### The three concerns

- **Reliability** - the system continues to work *correctly* (right function at the desired performance) even in the face of adversity (hardware faults, software errors, human mistakes). A *fault* is one component deviating from spec; a *failure* is the system as a whole stopping service. You cannot drive faults to zero, so you design *fault-tolerance* mechanisms that stop faults from becoming failures. Deliberately injecting faults (a *chaos monkey*) keeps the tolerance machinery exercised.
- **Scalability** - the system's ability to cope with increased load. It is not a binary label; it is the question "if load grows in a particular way, what are our options for coping?" You describe load with *load parameters* (requests/sec, read:write ratio, active users, fan-out), describe performance with *throughput* (batch) or *response-time percentiles* (online), then reason about what happens as the parameters grow.
- **Maintainability** - most of a system's cost is ongoing maintenance, not initial build. Three sub-goals: *operability* (make life easy for operations through good monitoring, automation, and predictable behavior), *simplicity* (manage complexity by removing accidental complexity through good abstractions), and *evolvability / plasticity* (make change easy as requirements shift).

### Faults and their classes

- **Hardware faults** - disks (MTTF 10–50 years; on a 10,000-disk cluster expect ~one death per day), RAM, power, networking. Traditionally mitigated by component redundancy (RAID, dual PSUs); increasingly handled by software fault-tolerance that tolerates loss of whole machines, which also enables rolling maintenance without downtime.
- **Software errors** - systematic, correlated faults (a bad-input crash that hits every node, a runaway process, a dependency degrading, cascading failures). Harder to anticipate; cause more failures than uncorrelated hardware faults. Mitigations: careful assumptions, testing, process isolation, monitoring, and self-checking invariants.
- **Human errors** - the leading cause of outages. Mitigations: well-designed abstractions/APIs that make the right thing easy, sandbox environments, thorough automated testing, fast rollback and gradual rollout, and detailed monitoring/telemetry.

### Describing performance

- Latency varies per request; treat it as a *distribution*, not a single number. Report **percentiles** (median/p50, p95, p99, p99.9) rather than the mean, which outliers distort. High percentiles (tail latency) matter most for backend services called many times per end-user request, because the slowest parallel call gates the whole request (*tail latency amplification*). Compute percentiles efficiently over rolling windows (e.g., forward decay); averaging percentiles across machines is statistically meaningless.

### Scaling approaches

- **Scaling up (vertical)** - one bigger machine; simplest for stateful systems.
- **Scaling out (horizontal / shared-nothing)** - distribute across many smaller machines; introduces distributed-systems complexity, especially for stateful data.
- Good architectures mix both pragmatically. There is no generic "magic scaling sauce"; a scalable architecture is built around assumptions about which operations are common (the load parameters) and is therefore application-specific.

## Frameworks & models

### Data models and query languages

- **Relational model** - data in relations (tables) of tuples (rows); good for many-to-many relationships and joins; declarative SQL lets the optimizer choose execution. Dominant for OLTP for decades.
- **Document model** - self-contained JSON/document trees; good *query locality* (one read fetches a whole tree), schema flexibility (*schema-on-read*), and a closer match to application objects (reduces the object-relational mismatch). Weak for many-to-many relationships and joins, which push work back into the application.
- **Graph model** - vertices and edges; ideal when many-to-many relationships and traversals dominate. Two main flavors: the *property graph* (Neo4j; queried with Cypher) and the *triple-store / RDF* model (queried with SPARQL); Datalog is the underlying foundation. Graph queries can also be expressed in recursive SQL but far more awkwardly.
- **Convergence** - relational and document databases are converging (relational DBs add JSON columns; document DBs add joins). Choose the model that fits the data's relationship structure; for highly interconnected data, graph wins.
- **Declarative vs imperative** - declarative languages (SQL, Cypher, SPARQL, CSS) say *what* you want and let the engine optimize and parallelize; imperative code (MapReduce's map/reduce functions sit in between) says *how*. Declarative is more concise and more parallelizable.

### Storage and retrieval (storage engines)

- **Log-structured engines (LSM-trees)** - writes append to an in-memory sorted structure (*memtable*); when full it is flushed as an immutable *SSTable* (Sorted String Table) on disk; background *compaction* merges SSTables and discards superseded keys; a write-ahead log gives crash recovery; *Bloom filters* speed up "key not present" lookups. Sequential writes give very high write throughput. Used by LevelDB, RocksDB, Cassandra, HBase, Lucene's index. Trade-off: read and compaction overhead, write amplification from repeated merging, and compaction can interfere with latency under heavy load.
- **Page-oriented engines (B-trees)** - the standard index in almost all relational databases. Data lives in fixed-size pages (~4 KB) arranged as a balanced tree of height O(log n); updates overwrite pages in place; a write-ahead (redo) log protects against crashes during a page split; concurrency needs latches. Trade-off: writes at least twice (WAL + page), slower random writes, but predictable read performance and natural support for strong transactional semantics.
- **B-trees vs LSM-trees** - LSM generally has higher write throughput and better compression; B-trees give more predictable read latency and easier locking for transactions. Neither universally wins; it depends on workload.
- **OLTP vs OLAP** - *transaction processing* (OLTP) handles small, key-based, low-latency reads/writes from user-facing apps and stores current state. *Analytic processing* (OLAP) aggregates over huge numbers of records for business intelligence and stores history. They diverged into separate systems: OLTP databases and *data warehouses* (loaded via *ETL*). Warehouses use *star/snowflake schemas* (a central fact table referencing dimension tables).
- **Column-oriented storage** - for analytics, store values column-by-column rather than row-by-row, so a query reads only the columns it needs. Enables strong compression (e.g., bitmap encoding), vectorized/pipelined execution, and multiple sort orders; materialized views and data cubes precompute aggregates. Writing is harder (LSM-style buffering helps).

### Encoding and evolution

- Data must be *encoded* (serialized) for storage or transmission and *decoded* on read. Rolling upgrades and client/server skew mean old and new code, and old and new data, coexist, so encodings must support **backward compatibility** (new code reads old data) and **forward compatibility** (old code reads new data).
- Language-specific serialization is convenient but brittle and insecure. Textual JSON/XML/CSV are interoperable but loose on schemas and numbers. **Binary schema-driven formats** (Thrift, Protocol Buffers, Avro) are compact and evolvable: fields carry tags or are matched by writer/reader schemas, so adding optional fields keeps both compatibility directions. Avro's writer/reader-schema resolution suits dynamically generated schemas well.
- Dataflow modes: through databases (the encoding outlives any one process), through service calls (REST and RPC; RPC tries to make a remote call look local but the network breaks that illusion), and through asynchronous message-passing (message brokers/actors decouple sender and receiver).

## Patterns

(Also recorded in PATTERN-CATALOG.md.)

- **Single-leader replication** - Intent: keep replicas in sync by routing all writes through one leader that streams a replication log to read-only followers. When to use: read-heavy workloads needing strong-ish consistency and simple conflict-free writes. Trade-offs: simple and consistent for writes, but the leader is a write bottleneck and a failover hazard (replication lag causes stale reads; failover risks lost writes and split brain).
- **Multi-leader replication** - Intent: accept writes at several leaders (per datacenter, per device, per collaborator) that replicate to each other. When to use: multi-datacenter operation, offline-capable clients, collaborative editing. Trade-offs: better write availability and locality, but write conflicts are inevitable and must be detected and resolved (LWW, version vectors, CRDTs, or application logic).
- **Leaderless replication (Dynamo-style)** - Intent: clients (or a coordinator) write to and read from many replicas directly, using quorums. When to use: high availability and write availability across failures (Cassandra, Riak, Voldemort). Trade-offs: tolerant of node failure and tunable, but consistency is best-effort; needs *read repair* and *anti-entropy* to converge, and quorum guarantees are probabilistic.
- **Quorum reads and writes (w + r > n)** - Intent: guarantee read/write overlap so reads see the latest write by requiring writes to w replicas and reads from r of n, with w + r > n. When to use: leaderless systems trading consistency vs availability. Trade-offs: tunable durability/latency, but edge cases (concurrent writes, sloppy quorums, hinted handoff) still allow stale or conflicting reads.
- **Read-your-writes / monotonic-reads / consistent-prefix-reads** - Intent: targeted consistency guarantees that paper over replication lag for the cases users notice (seeing your own write, never reading older data than before, never seeing effects before their causes). When to use: asynchronously replicated read replicas. Trade-offs: cheaper than full linearizability; each guarantee needs specific routing or tracking.
- **Hash / range partitioning (sharding)** - Intent: split a dataset across nodes by key to scale storage and throughput. When to use: data or load exceeds one node. Trade-offs: range partitioning gives efficient range scans but risks hot spots; hash partitioning spreads load but loses range queries. Secondary indexes are partitioned either by document (local, scatter-gather reads) or by term (global, write fan-out).
- **Consistent hashing / rebalancing** - Intent: move as little data as possible when nodes are added or removed. When to use: elastic clusters. Trade-offs: fixed-partition-count and dynamic schemes balance operational simplicity against flexibility; avoid automatic rebalancing during failures.
- **Two-phase commit (2PC)** - Intent: atomic commit across multiple nodes via a coordinator's prepare/commit rounds. When to use: distributed transactions needing atomicity across systems. Trade-offs: provides atomicity but blocks if the coordinator fails (the coordinator is a single point of failure); slow and operationally fragile.
- **Total order broadcast (atomic broadcast)** - Intent: deliver the same messages to all nodes in the same order. When to use: building linearizable storage, replicated state machines, and consensus. Trade-offs: equivalent in power to consensus; strong ordering at the cost of coordination and reduced availability under partitions.

## Principles & best practices

- Build reliable systems from unreliable parts; design for fault tolerance rather than fault prevention, and rehearse failures deliberately.
- Treat performance as a distribution; track tail percentiles, not averages, and watch tail-latency amplification in service meshes.
- Keep stateful systems on one node until cost or availability forces distribution; distributing state is where the hard problems begin.
- Choose the data model by the shape of the relationships, not by fashion: relational/document for tree-like or tabular data, graph for highly interconnected data.
- Design encodings for both backward and forward compatibility from the start; only add optional fields and never repurpose tags.
- Prefer asynchronous replication for availability and synchronous (or quorum) for durability, and make the trade-off explicit; understand the consistency guarantee your replication scheme actually provides.
- Make irreversible operations rare; favor idempotent writes (client-supplied keys) so retries on an unreliable network are safe.

## Anti-patterns & pitfalls

- **Assuming the network is reliable, latency is zero, bandwidth is infinite, topology is static, and clocks are synchronized** - the classic distributed-computing fallacies. Networks drop, delay, and reorder; partitions happen; *time-of-day clocks* jump and drift, so timestamps must never be trusted for ordering of events across machines (use logical clocks / version vectors instead).
- **Trusting wall-clock timestamps for conflict resolution** - last-write-wins silently drops concurrent writes; use version vectors or CRDTs when correctness matters.
- **Ignoring replication lag** - leads to users not seeing their own writes, time moving backwards, and causality violations. Add the specific consistency guarantee the use case needs.
- **Hot spots / hot keys in partitioning** - a celebrity key or sequential key (timestamp) overloads one shard; add randomness or compound keys.
- **Distributed transactions everywhere** - 2PC's blocking and coordinator coupling make heavyweight cross-service ACID a poor default; prefer sagas with compensations where eventual consistency is acceptable.
- **Mistaking eventual consistency for "no consistency"** - know exactly which anomalies (dirty reads/writes, lost updates, write skew, phantoms) your isolation level permits.
- **Conflating fault with failure** - a single deviating component need not bring down the system if tolerance is designed in.

## Decision guidance

- **Model selection:** tree-shaped, mostly-self-contained data with few cross-references → document. Many-to-many relationships, ad-hoc joins, strong integrity → relational. Densely interconnected, traversal-heavy data → graph.
- **Storage engine:** write-heavy ingestion, log/time-series, need for compression → LSM-tree. Read-latency-sensitive, transaction-heavy, range queries → B-tree. Analytics over wide tables → column store in a warehouse, fed by ETL/CDC, kept separate from OLTP.
- **Replication topology:** single datacenter, read scaling, simple correctness → single-leader. Multi-datacenter, offline clients, collaboration → multi-leader (plan conflict resolution). Maximum availability, tunable consistency → leaderless with quorums.
- **Consistency target:** need a single up-to-date value and linearizability (locks, leader election, uniqueness) → pay for consensus/total-order broadcast and accept reduced availability under partitions (CAP: when partitioned, choose consistency *or* availability). Can tolerate staleness → eventual consistency with targeted guarantees (read-your-writes, monotonic reads).
- **Transactions:** within one node, lean on ACID and the strongest practical isolation (serializable via SSI or actual serial execution when throughput allows). Across nodes/services, prefer sagas and idempotency over 2PC unless atomic cross-system commit is truly required.
- **Batch vs stream:** bounded, known dataset, latency-tolerant, high throughput → batch (MapReduce/Spark-style; the *dataflow* of immutable inputs to derived outputs is its strength). Unbounded, low-latency, event-driven → stream processing over an append-only log (the log is the source of truth; derived views are reproducible). Treating data as immutable event streams and deriving everything else (*event sourcing*, *CDC*) unifies the two.

## Source attribution

- The three concerns, faults (hardware/software/human), reliability and chaos engineering, load parameters, percentiles and tail-latency amplification, scaling up vs out: *DDIA* Ch. 1 (Reliable, Scalable, Maintainable Applications).
- Relational vs document vs graph models, object-relational mismatch, schema-on-read, declarative vs imperative queries, Cypher/SPARQL/Datalog, MapReduce querying, model convergence: *DDIA* Ch. 2 (Data Models and Query Languages).
- Hash indexes, SSTables/LSM-trees, B-trees, comparison, secondary/multi-column/fuzzy indexes, OLTP vs OLAP, data warehousing/ETL, star and snowflake schemas, column-oriented storage and compression: *DDIA* Ch. 3 (Storage and Retrieval).
- Single-leader/multi-leader/leaderless replication, synchronous vs asynchronous, failover, replication-log implementations, replication-lag anomalies and their fixes, write-conflict handling, quorums, read repair, anti-entropy: *DDIA* Ch. 4 (Replication) and the shared-nothing / partitioning material.
- Encoding and evolution (Thrift/Protobuf/Avro, compatibility directions, dataflow modes), partitioning/sharding, transactions and isolation levels, distributed-systems troubles (unreliable networks and clocks), consistency and consensus (linearizability, CAP, total-order broadcast, 2PC), and batch/stream processing: drawn from Kleppmann's *DDIA* framework (the full first edition extends the early-release text used here through these chapters).

## Recent developments (2024–2026)
- **DDIA 2nd edition shipped (2026, Kleppmann + Chris Riccomini).** The book was thoroughly revised for a decade of change. The largest additions cover data systems for AI/ML: vector indexes for semantic search, DataFrames for training datasets, and batch systems for preparing training data. It also adds cloud-native data architecture coverage. This is the most significant single update to this topic's source. (O'Reilly, "Designing Data-Intensive Applications, 2nd Edition", 2026, https://www.oreilly.com/library/view/designing-data-intensive-applications/9781098119058/; Pragmatic Engineer interview, https://newsletter.pragmaticengineer.com/p/designing-data-intensive-applications)
- **MapReduce demoted to a teaching device.** The 2nd edition cuts most MapReduce coverage because Spark and Flink have effectively replaced it; MapReduce now appears only as a learning model for partitioned batch processing. (Medium/Javarevisited review of DDIA 2e, 2025, https://medium.com/javarevisited/i-read-designing-data-intensive-applications-2nd-edition-and-its-awesome-417103df5aab)
- **Vector databases became a first-class building block.** Driven by RAG and semantic search, vector indexes (HNSW-style ANN) are now standard infrastructure in data-intensive design, a category that barely existed when the early-release text used here was written. (ScyllaDB, "Rethinking Designing Data-Intensive Applications", 2026, https://www.scylladb.com/2026/03/26/rethinking-designing-data-intensive-applications/)
- **AI inference reshapes scalability reasoning.** 2025-2026 system design increasingly centers on LLM-serving concerns the original framework does not address: disaggregated prefill/decode serving, semantic caching of similar queries, request batching for cost, and GPU/accelerator capacity planning, layered on top of the classic load-parameter and percentile thinking. (computer.org, "Disaggregating LLM Infrastructure", 2025/2026; arXiv, "GreenLLM", 2025, https://arxiv.org/pdf/2508.16449)
- **Core framework remains the field's anchor.** The three concerns (reliability, scalability, maintainability), replication/partitioning/consistency models, encoding compatibility, and the log-as-source-of-truth dataflow view are reaffirmed, not overturned, in the 2nd edition.
