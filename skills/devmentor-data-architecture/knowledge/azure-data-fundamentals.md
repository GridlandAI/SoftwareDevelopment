# Azure Data Fundamentals

**Domain:** 08 - Data Architecture and Engineering
**Primary sources:** Azure Data Fundamentals (Pena, 2025)

## Overview

This topic covers the foundational concepts a software professional needs to reason about data on Azure: how data is represented (structured, semi-structured, unstructured), how it is stored (file formats, relational vs. non-relational databases, cloud vs. on-prem), what kinds of workloads run against it (transactional vs. analytical, batch vs. streaming), the Azure services that implement each capability, and the basics of governance. It maps closely to the Microsoft DP-900 certification scope.

These fundamentals matter because nearly every modern application produces and consumes data, and the cost, performance, and correctness of a system hinge on early architectural choices: relational or NoSQL, lake or warehouse, batch or streaming, managed PaaS or self-managed VM. Getting the workload/storage match wrong leads to poor performance, runaway cost, frustrated users, and missed opportunities. The recurring lesson is that there is no single best store - modern architectures layer multiple specialized stores and pick each for its workload.

## Core concepts

**The data spectrum.** Data ranges across three forms. **Structured** data fits rows and columns with a fixed schema (relational tables); strong on integrity and SQL querying, weaker on flexibility. **Semi-structured** data has flexible, self-describing structure (JSON, XML, Avro); nested and variable. **Unstructured** data has no predefined model (text, images, audio, video, logs). Choosing the representation shapes storage and processing for the life of the system. (Pena, 2025)

**File formats.** CSV (universal, simple, no schema/types), JSON (the de facto web/API and document-DB standard, nested), Parquet (columnar, the analytics powerhouse - efficient for scanning specific columns across many rows), and Avro (compact binary with schema evolution, ideal for streaming with Event Hubs/Stream Analytics). Format choice drives query efficiency and schema-evolution flexibility. (Pena, 2025)

**Database types.** **Relational** databases enforce schemas and ACID guarantees - the reliable foundation for transactional integrity. **NoSQL** databases trade rigid schema for flexibility and scale, in four families: document, key-value, wide-column, and graph. **Data warehouses** are analytics specialists that organize data into dimensional (star/snowflake) models with columnar storage and MPP for fast aggregate queries. (Pena, 2025)

**Cloud vs. on-premises.** Cloud storage offers elasticity, managed infrastructure, geo-redundancy, and pay-as-you-go cost; on-prem offers maximum control and can suit fixed predictable workloads or strict data-residency needs. Azure hybrid solutions bridge the two. The choice balances capital vs. operational cost, control vs. convenience, and compliance. (Pena, 2025)

**ACID.** Atomicity, Consistency, Isolation, Durability - the transaction guarantees that make OLTP systems reliable (money isn't lost mid-transfer; the last item isn't oversold under concurrency). (Pena, 2025)

**ETL vs. ELT.** Traditional ETL transforms data before loading into a structured store (consistency, less flexibility). Modern ELT loads raw data first (often into a lake), then transforms per analytical need - preserving the original and enabling diverse downstream processing paths. (Pena, 2025)

## Frameworks & models

**The workload spectrum (OLTP ↔ OLAP).** Workloads span pure transactional to pure analytical. **OLTP** (Online Transaction Processing) handles many short, immediate, ACID-consistent, high-concurrency operations on current operational data (restaurant POS, banking, e-commerce checkout); optimized for fast individual transactions. **OLAP** (Online Analytical Processing) runs complex queries scanning millions of historical records to produce trends and strategic insight (year-over-year analysis, forecasting); optimized for analytical depth, tolerates minutes-to-hours latency. Most real systems are hybrids; **HTAP** (Hybrid Transactional/Analytical Processing, e.g., Cosmos DB analytical store) runs analytics on operational data without impacting transactions. Getting this distinction right drives database selection and architecture. (Pena, 2025)

**Modern Azure data roles.** Cloud shifted roles from infrastructure management to service orchestration. **Database administrators** moved from hardware/backups to automated policies, geo-replication, security/compliance, and cost optimization over managed services (Azure SQL Database, Cosmos DB, PostgreSQL/MySQL). **Data engineers** (evolved ETL developers) architect scalable pipelines with Data Factory, Databricks, Synapse, and Fabric; own data quality, lineage, and governance. **Data analysts** model and visualize in Power BI for descriptive analytics. **Data scientists** build predictive/ML models with Azure Machine Learning and Databricks. **Data governors/stewards** run governance, cataloging, classification, and compliance with Microsoft Purview. (Pena, 2025)

**The Azure SQL family (control spectrum).** Three options trade control for managed convenience: **Azure SQL Database** (fully managed PaaS / DBaaS - Microsoft runs everything below the database; cloud-native, automated maintenance, built-in HA; single database or elastic pools); **Azure SQL Managed Instance** (the bridge to cloud - near-full SQL Server compatibility incl. SQL Agent, Service Broker, linked servers, deployed in your VNet; ideal for lift-and-shift migrations with minimal code change); **SQL Server on Azure VMs** (IaaS - maximum control over OS and SQL config, with full management responsibility). Azure also offers managed open-source engines: Database for PostgreSQL and MySQL. (Pena, 2025)

**Cosmos DB APIs and consistency.** Azure Cosmos DB is a globally distributed, multi-model NoSQL service. One core engine fronts five APIs: **NoSQL/Core** (native, JSON, SQL-like queries - default for greenfield apps), **MongoDB** (lift-and-shift existing MongoDB apps), **Cassandra** (wide-column, high-write time-series/IoT), **Table** (upgrade path from Azure Table Storage), and **Gremlin** (graph). It offers five tunable consistency levels along the strong↔availability spectrum: **strong** (all readers see latest - banking balances), **bounded staleness** (lag bounded by time/operations), **session** (a user always sees their own writes - most practical for user-centric apps), **consistent prefix** (never out-of-order - chat ordering), and **eventual** (highest availability - likes/view counts). Data modeling favors denormalization/embedding around access patterns over normalized tables. (Pena, 2025)

**Large-scale analytics architecture.** A layered flow: diverse **sources** → **ingestion** (batch + streaming) → **storage** (data lake / warehouse) → **processing** (distributed) → **serving** → **consumption**, with governance, security, orchestration, and monitoring spanning all layers. The "V"s of big data - Volume, Variety, Velocity, plus Veracity and Value - define when traditional tools no longer suffice. (Pena, 2025)

**Layered analytical store (zones).** Most architectures combine stores: a **raw zone** (lake holds original data), a **refined zone** (processed, optimized formats in Data Lake Storage), a **curated zone** (structured warehouse/analytical DB), and **specialized zones** (purpose-built DBs). This gives both a lake's flexibility and a warehouse's performance. Databricks expresses the same idea as the **medallion architecture** (bronze/silver/gold). (Pena, 2025)

## Patterns

**Data lake** - *Intent:* preserve raw data in native format for maximum future analytical flexibility. *When to use:* evolving/unknown analytical requirements, data-science exploration, diverse formats, cost-efficient single-copy storage. *Trade-offs:* without governance/cataloging it becomes a "data swamp"; transformation responsibility shifts to consumers, risking inconsistent interpretation. (Pena, 2025)

**Data warehouse** - *Intent:* fast, consistent analytical queries over well-understood, structured data. *When to use:* standardized reporting, dashboards, BI with stable, well-defined requirements. *How:* star/snowflake schema (fact + dimension tables), columnar storage, MPP, partitioning/indexing. *Trade-offs:* schema-on-write delays data availability and reduces flexibility for exploration or rapidly changing sources; weaker for unstructured data. (Pena, 2025)

**Specialized analytical databases** - *Intent:* optimize for a specific analytical pattern. *Variants:* in-memory (interactive speed), columnar (column-scan analytics), time-series (IoT/financial/monitoring), graph (fraud, recommendations, network/impact analysis). *Trade-offs:* purpose-built strength but added architectural complexity; often used alongside lakes/warehouses. (Pena, 2025)

**Batch ingestion/processing** - *Intent:* process data in scheduled chunks with thoroughness and validation. *When to use:* historical loads, complex transformations, initial datasets, where latency tolerance exists. *Trade-offs:* not suited to time-sensitive insight; throughput-oriented, not latency-oriented. (Pena, 2025)

**Streaming ingestion/processing** - *Intent:* process data continuously as it is generated for immediate value. *When to use:* anomaly/fraud detection, real-time personalization, monitoring/alerting, IoT telemetry. *How (Azure):* Event Hubs (massive event ingestion + partitioning + temporary retention + pub/sub) → Stream Analytics (SQL-like persistent queries with windowing, reference-data joins, temporal processing). *Trade-offs:* Stream Analytics is limited on complex multi-way joins and ML - use Databricks Spark Structured Streaming for those. (Pena, 2025)

**Choosing an analytics platform** - *Intent:* match the engine to skills and workload. *Options:* **Azure Databricks** (Spark, code-first, data science/ML, lakehouse + medallion, MLflow, Delta Lake); **Azure Synapse Analytics** (integrated SQL dedicated pools (MPP) + Spark pools + data integration + serverless lake querying under one Studio; evolved from SQL Data Warehouse); **Microsoft Fabric** (unified SaaS over OneLake, minimal admin, end-to-end self-service for all personas, launched 2023). *Trade-offs:* Databricks for Spark/ML depth, Synapse for SQL warehousing breadth, Fabric for simplicity and integration; many organizations use all three for different workloads. (Pena, 2025)

## Principles & best practices

- **Match the store to the workload.** No single technology excels at all scenarios; layer lakes, warehouses, and specialized databases and route each workload to the right store. (Pena, 2025)
- **Separate transactional and analytical systems.** Serving both OLTP and OLAP from one system compromises performance for one or both; keep them separate even when they share data. (Pena, 2025)
- **Prefer managed services where possible.** Azure SQL Database, Cosmos DB, and managed open-source engines remove infrastructure toil so teams focus on data and applications. Choose the lowest-control option that meets compatibility needs (DB → Managed Instance → VM). (Pena, 2025)
- **Tune consistency to the use case.** Cosmos DB lets you pick consistency per need - use strong only where correctness demands it (money), session for user-centric apps, eventual for tolerant counters; stronger consistency costs latency/availability. (Pena, 2025)
- **Govern the lake from day one.** Use metadata management, cataloging (Microsoft Purview), and curated zones to keep raw lakes from becoming swamps; document lineage and classify sensitive data. (Pena, 2025)
- **Design ingestion for both batch and streaming.** Modern landscapes mix high-velocity streams and periodic batches; hybrid ingestion (Data Factory for batch, Event Hubs/IoT Hub for streams) handles both while preserving completeness. (Pena, 2025)
- **Adopt incremental, value-delivering rollout.** Stand up the lake and priority pipelines first, then add real-time, then advanced ML, then IoT - proving business value at each phase and learning before the next. (Pena, 2025)
- **Embed security and compliance.** Encryption at rest/in transit, access control via Entra ID/RBAC, and compliance with GDPR/CCPA/HIPAA are foundational responsibilities across DBA, engineer, and governor roles. (Pena, 2025)

## Anti-patterns & pitfalls

- **Forcing all data into one system or approach.** Scaling up a single traditional database for petabyte, multi-format, high-velocity data inevitably falters; large-scale analytics requires distributed, specialized architectures. (Pena, 2025)
- **Data swamps.** Raw lakes without governance, cataloging, and curated zones become disorganized repositories where valuable data is unfindable and inconsistently interpreted. (Pena, 2025)
- **Assuming OLTP and OLAP are interchangeable.** The distinction is about optimization and primary use case; mismatching workload to store causes poor performance and cost. (Pena, 2025)
- **Choosing on-prem-style control by default.** SQL Server on a VM means owning patching, HA, and tuning; reach for PaaS unless specific compatibility or control needs require IaaS. (Pena, 2025)
- **Over-strong consistency.** Defaulting Cosmos DB to strong consistency everywhere needlessly sacrifices latency and availability for data that tolerates staleness. (Pena, 2025)
- **ETL-only thinking for modern sources.** Rigid nightly ETL can't keep up with continuous high-velocity streams (causing data loss and stale insight); adopt ELT and streaming where appropriate. (Pena, 2025)

## Decision guidance

- **Relational vs. non-relational:** Choose relational for well-defined schemas, transactional integrity, and SQL reporting. Choose NoSQL (and the right Cosmos DB API) for flexible/evolving schemas, global scale, and access-pattern-driven models - document for variable JSON, wide-column for high-write time-series/IoT, graph for relationship analysis, key-value/table for simple high-scale lookups. (Pena, 2025)
- **Lake vs. warehouse:** Lake for raw, diverse, exploratory, evolving data and data science; warehouse for structured, well-understood, repeatable BI/reporting. Most architectures use both in a layered (raw → refined → curated) design. (Pena, 2025)
- **Batch vs. streaming:** Batch when latency is tolerable and transformations are complex/historical; streaming when immediate action adds value (fraud, personalization, monitoring). Combine both (lambda-style) for systems needing both fresh and comprehensive results. (Pena, 2025)
- **Azure SQL option:** Azure SQL Database for new cloud-native apps wanting minimal management; SQL Managed Instance to migrate existing SQL Server with server-level features and minimal code change; SQL Server on Azure VM only when full OS/engine control is required. (Pena, 2025)
- **Analytics platform:** Databricks for Spark-based engineering and ML, Synapse for SQL-centric warehousing plus integrated Spark, Fabric for a unified low-admin SaaS experience and self-service. Let existing team skills and integration needs guide the choice; adopt multiple if workloads differ. (Pena, 2025)
- **Choosing an analytical store overall:** Weigh data structure, query patterns (known/repeatable vs. exploratory), performance requirements (in-memory/specialized vs. batch), and integration/skills; default to a layered multi-store design rather than one technology. (Pena, 2025)

## Source attribution

- **Data representation spectrum, file formats, database types, cloud vs. on-prem, ACID:** Azure Data Fundamentals, Ch. 1–2 (Pena, 2025).
- **OLTP/OLAP workload spectrum, HTAP, batch vs. streaming characteristics:** Ch. 3 and Ch. 10 (Pena, 2025).
- **Modern Azure data roles and their tools:** Ch. 4 (Pena, 2025).
- **Relational concepts and the Azure SQL family (Database, Managed Instance, VM, open-source engines):** Ch. 5–6 (Pena, 2025).
- **Azure storage solutions and Cosmos DB APIs/consistency models:** Ch. 7–8 (Pena, 2025).
- **Large-scale analytics architecture, data ingestion/processing, analytical stores, Databricks/Synapse/Fabric, layered/medallion zones:** Ch. 9 (Pena, 2025).
- **Real-time analytics services (Event Hubs, Stream Analytics, Data Explorer/KQL, Event Grid, Functions, IoT Hub, Synapse Link):** Ch. 10 (Pena, 2025).

## Recent developments (2024–2026)

- **Fabric absorbed operational databases, blurring the OLTP/OLAP and store-selection lines.** Fabric SQL database and Cosmos DB in Fabric went GA (Nov 2025), provisioning in seconds with data auto-mirrored into OneLake, so transactional and NoSQL stores now sit beside analytics in one SaaS tenant, a concrete instance of this file's HTAP / layered-store guidance. (Microsoft Fabric Blog, "GA of Cosmos DB in Microsoft Fabric," 2025, https://blog.fabric.microsoft.com/en-US/blog/announcing-ga-of-cosmos-db-in-microsoft-fabric-and-cosmos-db-mirroring/)
- **Cosmos DB added native vector search.** DiskANN-based vector indexing/search in Cosmos DB lets embeddings live next to operational JSON, supporting RAG and AI workloads directly on an operational store, extending the NoSQL-API guidance with an AI use case. (Microsoft Fabric Blog, 2025, link above; DataCamp, "Best Vector Databases 2026," 2026, https://www.datacamp.com/blog/the-top-5-vector-databases)
- **Mirroring became the default zero-ETL ingestion pattern.** GA mirroring for Azure SQL, PostgreSQL, Cosmos DB, and SQL Server continuously replicates operational data into OneLake without pipelines, reinforcing the ELT/streaming-first direction this file describes over rigid nightly ETL. (Microsoft Fabric Blog, "What's new in OneLake and the Fabric platform," 2025, https://blog.fabric.microsoft.com/en-us/blog/whats-new-in-onelake-and-the-fabric-platform-more-sources-security-and-capacity-tooling)
- **Governance (Microsoft Purview) became AI-centric.** Purview's 2025 investments integrated governance into the OneLake catalog and extended data-security/compliance controls to Copilot interactions (discovering sensitive data in prompts/responses), deepening the "govern the lake from day one" principle for the AI era. (Microsoft Security Blog, "Microsoft Purview innovations for your Fabric data," 2025, https://www.microsoft.com/en-us/security/blog/2025/09/16/microsoft-purview-innovations-for-your-fabric-data-unify-data-security-and-governance-for-the-ai-era/)
- **Copilot embedded AI assistance across the data roles.** Copilot in Fabric and Azure Data Factory now offers code completion, transformation suggestions, and natural-language dataflow generation, shifting parts of the data-engineer and analyst roles toward AI-augmented authoring. (Microsoft Learn, "Overview of Copilot in Fabric," 2025, https://learn.microsoft.com/en-us/fabric/fundamentals/copilot-fabric-overview)
