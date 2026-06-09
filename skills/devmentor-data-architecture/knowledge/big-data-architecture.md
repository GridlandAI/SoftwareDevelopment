# Big Data Architecture

**Domain:** 08 - Data Architecture and Engineering
**Primary sources:** Scalable Big Data Architecture (Azarmi; Apress, 2015); Modern Data Architecture on Azure (Lad; Apress, 2023); The Cloud Data Lake (Gopalan et al.; O'Reilly, 2023)

## Overview

Big data architecture is the discipline of designing systems that ingest, process, and serve data at a volume, velocity, and variety that traditional relational/ETL-into-warehouse pipelines cannot handle economically. The traditional pattern - periodic ETL (Informatica, Talend) extracting from CRM/file/legacy systems running on Oracle, DB2, or SQL Server, then loading digested data into a warehouse - fails on two fronts: latency (pipelines take hours, days, or weeks while decisions are needed now) and scalability (scaling a non-distributed RDBMS is expensive in hardware and deployment time). The modern answer distributes processing across commodity machines and ingests large amounts of heterogeneous data.

Big data architecture matters in the SDLC because it sets the processing topology - how batch and stream work are split, buffered, and merged - that downstream analytics, ML, and serving depend on. The defining design decision is matching the architecture to the **SLAs**: different jobs have different latency requirements, so resources must be prioritized or isolated. This file covers the conceptual ingestion/processing/serving layers, the lambda and kappa architectures, batch vs. stream processing, and the Hadoop/Spark/Elastic ecosystems at a conceptual level.

## Core concepts

- **Variety of sources.** Big data flows from internal application and device logs, social networks, open data, and sensors, in structured, semi-structured, or unstructured form. The challenge is that useful information drowns among massive amounts of useless data, so data must first be stored and then processed to extract the valuable parts (e.g., sentiment analysis over social feeds). (Azarmi)

- **Splitting the architecture by SLA.** Modern data architecture is split into a **long-term (batch) processing** part, a **short-term (stream) processing** part, and a **view-merging (serving)** part. Every job should not consume resources equally; jobs are prioritized (e.g., via YARN Capacity Scheduler queues) or isolated by hardware/network. This SLA-driven split is the organizing idea behind lambda architecture. (Azarmi)

- **Batch processing.** Long-running jobs (first-generation Hadoop: MapReduce, Hive, Pig) that process massive datasets into digested aggregates for analytics. Easy to manage and monitor because they run "in one shot" rather than continuously. YARN makes resource allocation and multitenant prioritization straightforward. (Azarmi)

- **Stream processing.** Short-term jobs that ingest high-throughput data with low latency, demanding high availability, resilience, and performance. Must integrate many sources (warehouses, files, social networks, app logs) and emit to many channels (notification engines, search, third-party apps). Delivers real-time analytics: discover data as it arrives, query easily, and alert proactively when an event crosses a threshold. First used in high-frequency trading, then e-commerce, telecom, fraud detection, and analytics. Driven the rise of Apache Spark and Apache Storm. (Azarmi)

- **Real-time vs. near-real-time ingestion.** *Real-time* requires continual input, processing, and output with immediate results (millisecond latency) - e.g., an ATM/card transaction or banking system. *Near-real-time* requires speed but not instantaneous results. Real-time processing must avoid clogging the ingestion pipeline and needs storage that supports high-volume writes. (Lad)

- **The four pillars (Lad).** A big data architecture's core components are data ingestion and processing, data analysis, data visualization, and data governance.

## Frameworks & models

### Lambda architecture

Lambda takes the best of both worlds by running two parallel processing paths and merging their results:

- **Batch layer.** Continuously feeds fresh data into the master dataset, controls the authoritative dataset, and precomputes comprehensive, accurate batch views on a schedule (e.g., once or twice a day). Built with ETL pipelines and conventional warehouses.
- **Speed layer.** Handles recent data not yet reflected in the batch view, providing real-time views to give a complete current picture and compensate for the batch layer's latency.
- **Serving layer.** Merges batch and speed views and serves them to consumers.

Azarmi's reference topology: Logstash-forwarder for ingestion, Apache Kafka to distribute data, a Logstash agent to process logs, Apache Spark for stream processing, and Elasticsearch as the serving layer (replacing Hive so analytics run in near real time). Common use cases: e-commerce recommendations (clickstream correlated with demographics, indexed into Elasticsearch) and real-time bot detection (analyze click behavior and blacklist bot IPs).

**Trade-offs (Lad).** Lambda balances latency, throughput, and fault tolerance, and supports "fast data" log-ingestion analytics. But its weaknesses are: a **fusion/dual-codebase problem** - two codebases (batch and stream) that must be kept consistent, making it hard to implement and maintain; residual **latency** depending on batch frequency; and high **resource consumption / cost** because the same data is processed twice through both layers.

### Kappa architecture

Kappa is a streaming-only simplification of lambda. All data is treated as a stream: incoming data is stored in a message engine (Apache Kafka) as an append-only, immutable, ordered log; a single **stream processing engine** reads, transforms, aggregates, and computes from it; results are written to an analytics store, a materialized table, or another stream. The same technology stack serves both real-time and historical/batch analytics - to recompute the full dataset you simply **replay the stream** (parallelized for speed), exactly as lambda's batch layer would recompute, but without a separate batch codebase.

Like lambda's batch layer, Kappa keeps event data immutable and complete (not sampled) in a distributed, fault-tolerant unified log where only new events change state. Azarmi's-style limitations are addressed: a single source of truth and a single processing layer.

**Trade-offs (Lad).** Simpler to set up and maintain than lambda (one system for batch and stream), eliminates synchronizing/reconciling two layers, scales up/down with demand, supports immediate reprocessing from stream jobs, and is cost-effective (no conventional data lake/batch system required). It handles "human fault tolerance" the same way lambda does - fix the processing code and rerun it on historical data. Best for on-demand and continuous-pipeline analytics, IoT, and real-time processing. The cost is that everything must fit a streaming model and a single engine.

**Azure realizations (Lad).** Lambda: Azure Stream Analytics (speed layer), ADLS + Azure Databricks (batch layer), Azure Synapse/SQL Data Warehouse (serving layer), with PolyBase views merging streamed and batch tables. Kappa: a unified ingestion layer processed by Azure Databricks with Delta Lake providing ACID storage so historical and streaming queries run on one pipeline.

### Ingestion / processing / serving layers (Azarmi)

A typical streaming (logging) architecture has three structural parts:

1. **Shipping (ingestion).** Agents next to the data sources receive and forward data. Common technologies: Apache Flume and Logstash. Flume uses a three-tier source → channel → sink structure and guarantees delivery from sources (Avro, Thrift, Syslog) to sinks (HDFS, log files, HBase, Elasticsearch) with horizontal scalability. Logstash is a single-block JRuby/JVM agent (receive, forward, forget) with a declarative input → filter → output pipeline; Logstash-forwarder ships logs to Logstash instances.
2. **Buffering.** A distributed message broker (RabbitMQ, Kafka, or Redis; Kafka preferred) persists data, decouples shipping from processing, and ensures no data is lost.
3. **Processing.** Either a lightweight pipeline that transforms, formats, validates, and enriches before persisting, or a Hadoop-based pipeline adding capabilities like machine learning. Results are indexed into a serving store (Elasticsearch) for search and analytics.

### The Hadoop / Spark / Elastic ecosystems (conceptual)

- **Hadoop ecosystem.** HDFS (distributed, replicated storage - the first-class citizen for data in a Hadoop cluster), MapReduce (batch programming model), and YARN (job scheduling and multitenant resource management via the Capacity Scheduler). Surrounding projects: Hive (SQL-like batch querying), Pig, Storm (real-time), Mahout, Sqoop (bulk transfer between RDBMS and HDFS/Hive/HBase), Impala (fast SQL on HDFS). Distributions: Cloudera, Hortonworks, and cloud PaaS (AWS EMR, Azure HDInsight). (Azarmi)
- **Spark.** In-memory, fault-tolerant engine (RDD abstraction) with a unified model for batch, SQL, ML (MLlib), streaming, and graph (GraphX) - the dominant choice for stream processing and the lambda speed layer. (Azarmi; Gopalan et al.)
- **Elastic (ELK).** Elasticsearch as the indexation/serving engine for near-real-time search and analytics, fed by Logstash and connected to Hadoop via ES-Hadoop, often replacing Hive in the serving layer. (Azarmi)

### Real-time processing logical components (Lad)

A real-time architecture comprises: real-time data ingestion (streaming input for time-sensitive decisions - Azure Event Hubs, Kafka, Stream Analytics); stream processing (consume, evaluate, filter, enrich a continuous stream, then forward to an app, store, or another engine); an analytical data store (a scalable, columnar, query-optimized database for advanced analytics); and reporting (analysis and reporting tools over the streaming data).

## Patterns

*(Lambda and kappa, plus the key big-data layering patterns below, are appended to `../_indexes/PATTERN-CATALOG.md`.)*

- **Lambda architecture** - Intent: combine a batch layer (accurate, comprehensive precomputed views) with a speed layer (low-latency views of recent data), merged in a serving layer, to balance latency, throughput, and fault tolerance. When to use: workloads needing both accurate historical aggregates and real-time views (recommendations, security/fraud analytics, log analytics). Trade-offs: completeness plus low latency vs. dual codebases that must stay consistent, residual latency, and double processing cost.
- **Kappa architecture** - Intent: treat all data as an immutable replayable stream processed by one engine, serving both real-time and historical analytics from a single codebase. When to use: streaming-first systems, IoT, continuous pipelines, on-demand analytics where a separate batch stack is unjustified. Trade-offs: simplicity, one source of truth, easy reprocessing via replay vs. everything must fit a streaming model and single engine.
- **SLA-driven architecture split (batch / stream / serving)** - Intent: divide processing by latency requirement and prioritize/isolate resources accordingly. When to use: multitenant data platforms with mixed-urgency workloads. Trade-offs: predictable resource sharing and SLA adherence vs. operational complexity of managing queues and isolation.
- **Shipping → buffering → processing ingestion pipeline** - Intent: decouple data producers from processors via a durable message broker so no data is lost and bursts are absorbed. When to use: any streaming ingestion at scale. Trade-offs: resilience and elasticity vs. an extra broker tier to operate and monitor.
- **Stream-first (streaming as the default)** - Intent: stream data first and only batch-store it if needed, rather than standing up a full Hadoop batch stack by default. When to use: most modern use cases where the same processing API serves both needs. Trade-offs: less hardware/software complexity vs. continuous operations and careful sizing to absorb unexpected data bursts.

## Principles & best practices

- Design to the **SLAs**: do not let every job consume resources equally; prioritize or isolate by latency need.
- **Decouple** shipping from processing with a durable, distributed message broker so bursts are absorbed and no data is lost.
- Prefer **stream-first** when the same processing API can serve both real-time and batch needs - it reduces hardware and software complexity.
- Use **YARN/Capacity Scheduler** (or equivalent) to make a shared platform safely multitenant with per-use-case capacity.
- Pick the serving store for the access pattern - a near-real-time search/analytics engine (Elasticsearch) can replace Hive when low-latency querying matters.
- Keep the immutable log (Kafka) as the **single source of truth** in kappa so recomputation is just a replay.
- Match the architecture to the data's nature: high-velocity immutable "fast data" (logs, clickstream) fits lambda/kappa well.

## Anti-patterns & pitfalls

- **Traditional ETL-into-warehouse for big data.** Hour-to-week pipeline latency misaligns with "decide now" business needs, and refactoring data models or scaling a non-distributed RDBMS is slow and expensive.
- **Lambda dual-codebase drift.** Maintaining separate batch and stream codebases and keeping them consistent is the chief lambda failure mode; consider kappa when the streaming engine can also recompute history.
- **Double processing cost.** Lambda processes the same data twice; on cloud this raises cost and resource consumption - weigh against kappa.
- **Clogging the ingestion pipeline.** Real-time processing that cannot keep up backs up ingestion; ensure storage supports high-volume writes and processing is fast enough.
- **Standing up Hadoop "just" to do what streaming can do.** Deploying a full batch distribution for needs a streaming architecture handles with the same (or richer) API is wasted effort.
- **Drowning in useless data.** Without filtering/enrichment, valuable signal is lost in volume - store first, then process to extract the important parts.

## Decision guidance

- **Lambda vs. kappa.** Choose **lambda** when you genuinely need a separate, batch-computed authoritative view with different logic than the stream path, and the cost/complexity of two codebases is justified by accuracy needs. Choose **kappa** when a single streaming engine can both serve real-time views and recompute history by replay - you get one codebase, one source of truth, lower complexity and cost, at the price of fitting everything into a streaming model.
- **Batch vs. stream vs. both.** Batch suits large precomputed aggregates with relaxed latency and is easy to monitor; stream suits low-latency, high-throughput, alerting workloads. Most real systems split by SLA and merge in a serving layer (lambda) - but lean stream-first when the same API covers both, storing to batch only if needed.
- **Ecosystem choice.** Use Hadoop/HDFS/YARN where heavy batch and a broad tool ecosystem are needed; Spark for unified in-memory batch+stream+ML; Kafka for durable buffering and the kappa log; Elasticsearch/ELK for near-real-time search and serving. On Azure, map these to Event Hubs/Stream Analytics (ingest/speed), Databricks + Delta Lake (processing/unified), and Synapse (serving).
- **Hadoop distribution choice (Azarmi).** Hybrid/multi-cloud → ISV distributions (Cloudera/Hortonworks) for portability; tight native integration → the cloud provider's PaaS (EMR, HDInsight, DataProc); cost-sensitive with a strong team → fork Apache Hadoop/Spark and self-build.

## Source attribution

- Traditional-architecture limitations, the SLA-driven batch/stream/serving split, batch and stream processing definitions, YARN multitenancy/Capacity Scheduler, the lambda architecture and its reference topology (Logstash-forwarder, Kafka, Logstash, Spark, Elasticsearch) and use cases, the shipping/buffering/processing ingestion pipeline (Flume, Logstash, Kafka/RabbitMQ/Redis), and the Hadoop/Spark/Elastic ecosystem at a conceptual level: **Azarmi (2015)**, Chapters 1, 3, 4.
- The lambda and kappa architectures with their layers, advantages, and limitations (dual-codebase, latency, resource cost vs. kappa's simplicity), real-time vs. near-real-time ingestion, the four pillars of big data architecture, real-time processing logical components, and the Azure realizations (Stream Analytics, Databricks, Delta Lake, Synapse, Event Hubs): **Lad (2023)**, Chapter 3.
- MapReduce, Hadoop (HDFS/YARN), and Spark (RDDs, unified model) foundations referenced conceptually: **Gopalan et al. (2023)**, Chapter 2 (see `data-lakes-and-lakehouse.md`).

## Recent developments (2024–2026)

- **Streaming engines added real-time and AI-native features, pushing toward kappa-style unification.** Spark 4.0 deepened batch/stream unification (AQE and Spark Connect improvements), and Spark 4.1 (Dec 2025) introduced Structured Streaming Real-Time Mode targeting millisecond latency alongside the existing seconds-latency micro-batch, narrowing the gap with Flink for the speed layer. (Modern Data 101, "Understand the New Spark Streaming Feature," 2025, https://moderndata101.substack.com/p/understand-the-new-spark)
- **Apache Flink 2.x reframed stream processing for the AI era.** Flink 2.0 simplified APIs and added a disaggregated state backend; Flink 2.2 (Dec 2025) brought native AI/ML inference in SQL and Process Table Functions bridging SQL and DataStream, removing the legacy DataSet API, reinforcing the stream-first default this file recommends. (Apache Flink, "Apache Flink 2.2.0," 2025, https://flink.apache.org/2025/12/04/apache-flink-2.2.0-advancing-real-time-data--ai-and-empowering-stream-processing-for-the-ai-era/)
- **The streaming lakehouse merged the speed and batch layers onto open table formats.** Kafka/Flink pipelines now write directly into Iceberg, Delta, Hudi, and Paimon, so a single open-table store serves both real-time and historical analytics, the practical realization of kappa's "replay the log" idea on durable lakehouse tables. (Kai Waehner, "Data Streaming Meets Lakehouse: Apache Iceberg," 2025, https://www.kai-waehner.de/blog/2025/11/19/data-streaming-meets-lakehouse-apache-iceberg-for-unified-real-time-and-batch-analytics/)
- **CDC and real-time movement displaced periodic batch as the default.** Data teams shifted away from scheduled batch ETL toward change data capture and continuous streaming, validating this file's critique of "traditional ETL-into-warehouse for big data." (Onehouse, "Comparing Stream Processing Engines," 2025, https://www.onehouse.ai/blog/apache-spark-structured-streaming-vs-apache-flink-vs-apache-kafka-streams-comparing-stream-processing-engines)
- **The lakehouse now extends to agentic AI and edge inference.** 2026 forecasts describe the lakehouse moving beyond a central repository to power real-time analytics, agentic AI, and edge inference, extending the serving layer's reach. (datalakehousehub.com, "2025 & 2026 Ultimate Guide to the Data Lakehouse," 2025, https://datalakehousehub.com/blog/2025-09-2026-guide-to-data-lakehouses/)
