# Data Lakes and the Lakehouse

**Domain:** 08 - Data Architecture and Engineering
**Primary sources:** The Cloud Data Lake (Gopalan, Roumeliotis, Leonard, Kwan; O'Reilly, 2023); Modern Data Architecture on Azure (Lad; Apress, 2023)

## Overview

A cloud data lake is a large-scale, low-cost, elastically scalable object-storage repository that holds structured, semi-structured, and unstructured data in its native form, decoupled from the compute engines that process it. Data lakes arose because traditional on-premises data warehouses could not economically store the volume, variety, and velocity of modern data, nor support exploratory data science and machine learning on raw, unstructured data. The defining characteristic of the cloud data lake architecture is the **disaggregation of compute and storage**: data is its own first-class tier, and many engines (Spark, Hive, Trino, warehouses) come to the data instead of the data being loaded into each engine.

The **lakehouse** is the convergence architecture that runs warehouse-style BI/SQL workloads directly on the data lake by adding open table formats (Delta Lake, Apache Iceberg, Apache Hudi) that bring ACID transactions, schema enforcement, and metadata management to files in object storage. This matters in the SDLC because the lake/lakehouse is the substrate beneath most analytics, ML, and data-mesh implementations: ingestion patterns, zone organization, file/table format choices, governance, and cataloging decisions made here determine the cost, performance, scalability, and trustworthiness of everything built on top.

## Core concepts

- **Variety of data (three categories).** *Structured* - rigid rows/columns with a strictly enforced predefined schema, stored in proprietary optimized binary formats (relational databases). *Semi-structured* - a loosely defined, flexible structure (JSON, XML) where new fields can be added without breaking existing readers. *Unstructured* - no restrictions (free-form text, images, video, PDFs); easiest to store, hardest to process (requires custom parsers). (Gopalan et al.)

- **Four building blocks of the cloud data lake architecture.** The data itself; the **data lake storage** (PaaS object storage - Amazon S3, Azure Data Lake Storage Gen2, Google Cloud Storage - supporting petabytes, hundreds of thousands of transactions/second, tiered storage, replication, and rich security); the **big data analytics engines** that process the data; and the **cloud data warehouse**.

- **Disaggregated compute and storage.** Unlike a warehouse where compute and storage are sold together and optimized as one, the lake stores data once and lets one or more stateless engines query it in place with no data movement. This is the root of the lake's cost-efficiency and scalability.

- **ELT vs. ETL.** Warehouses use **ETL** (extract, transform to the warehouse's format, then load) - a signal lost in transformation can be hard or impossible to recover. Lakes use **ELT** (extract, load raw into the lake, transform later with processing) - enabled by cheap storage and rising data value, preserving the raw signal for future use.

- **Schema-on-read vs. schema-on-write.** Warehouses enforce schema on write; lakes defer schema to read time (or are schema-free), supporting multiple schemas over the same data and "schema drift." Lakehouses blend both.

- **Metadata.** Data about the data - both schema descriptions and physical-layout information (which file chunk holds which rows/values) that compute engines read to fetch only relevant data. Table formats maintain their own metadata to enable ACID, data skipping, and engine handshakes. Distinct from **Master Data Management** (data about an organization's core entities - customers, products, locations).

## Frameworks & models

### The three cloud data lake architectures (Gopalan et al., Chapter 2)

- **Modern Data Warehouse architecture.** Data lake and cloud warehouse coexist. Raw data is ingested into the lake (cheap long-term store, supports data science/ML), transformed with Spark/Hadoop into high-value structured data, then loaded into a warehouse for BI/SQL dashboards. Benefits: familiar SQL/BI tooling plus new ML scenarios; lower cost from cheap lake storage. Challenges: two infrastructures to maintain; data-swamp risk; BI users depend on data engineers (a critical path); sharing between lake and warehouse requires copies in different formats/stores.

- **Data Lakehouse architecture.** A single platform runs both BI/SQL and data science/ML directly on the lake - no separate warehouse, no data movement, free sharing between BI and data-science teams. Made possible by open table formats. Benefits: lower cost, no silos, cross-pollination of insights. Challenges: requires careful assembly of the right table format + compute engine; warehouses give the optimized path out of the box but are not truly open.

- **Data Mesh architecture.** A decentralized distribution of lakes/warehouses across domains forming one logical data fabric (see `data-mesh.md`). Benefits: self-serve scalability, freedom of architecture per domain, data-as-culture avoiding bottlenecks. Challenges: relies on skilled domain teams; adds a distributed layer of complexity; nascent tooling.

### Data lake zones (the medallion model) (Gopalan et al., Chapter 3)

Following the lifecycle of data, the lake is organized into zones by stage of processing and value density:

- **Raw / Bronze zone.** Data lands here in its natural state from external sources (the "load" of ELT). Minimal structure, semantics, or value guarantee. Ingestion is typically controlled by a select team. Organized by source and then by timestamp (freshness).
- **Enriched / Silver zone.** Raw data is cleansed and prepared ("cooked") into a tabular structure. Three steps: *schema definition*, *data cleansing* (conform values to schema, fix or drop violations), and *optimization* (rewrite into a query-friendly columnar format like Parquet). Used by data scientists and some analysts; organized into domains and published to a catalog.
- **Curated / Gold zone.** Highest value density - aggregated, filtered, correlated data that powers key business dashboards. Highest data-quality standard. Sourced from enriched data plus occasional extra sources. Organized by business unit/domain and cataloged.
- **Workspace / Sandbox zone (optional).** A scratchpad for consumers to bring their own datasets for exploration; no quality guarantee; a candidate for data-retention policies to control growth.

### Lakehouse layers (Lad, Chapter 4)

A data lakehouse is structured in four layers: **Ingestion layer** (orchestrator integrates sources into the cloud), **Storage layer** (structured/semi/unstructured data in open formats - Parquet or ORC), **Metadata layer** (a unified catalog providing ACID transactions, file caching, indexing, schema enforcement, governance/auditing), and **Consumption layer** (secure, authenticated/authorized access for downstream users).

### Big data analytics engines (Gopalan et al., Chapter 2)

- **MapReduce.** The foundational programming model (Dean & Ghemawat): a *map* phase groups data by key into intermediate key/value pairs, a *reduce* phase processes those into filtered results - distributing huge datasets predictably across worker units.
- **Apache Hadoop.** Distributed processing built on MapReduce. Four components: Hadoop Common, **HDFS** (distributed storage), **Hadoop MapReduce**, and **YARN** (job scheduling and resource management). Spawned Hive, Pig, Storm, Mahout. Commercialized by Cloudera/Hortonworks and as cloud PaaS (AWS EMR, Azure HDInsight). Laid the foundation of the data lake.
- **Apache Spark.** A flexible engine with MapReduce's fault tolerance and scale plus support for iterative ML and real-time processing. Its core abstraction, **Resilient Distributed Datasets (RDDs)**, keeps intermediate data in memory (no persistent spill) while staying fault tolerant, greatly improving performance. Unified model: batch, Spark SQL, MLlib, Spark Streaming, GraphX. Runs on YARN, Mesos, or Kubernetes; offered by EMR, Azure Synapse, GCP DataProc, and Databricks.
- **Cloud data warehouses.** Managed PaaS warehouses: Amazon Redshift (cluster-based), Google BigQuery (serverless), Azure Synapse Analytics (unified cluster + serverless SQL/Spark), and Snowflake (disaggregated compute/storage across all clouds). A recent trend is warehouses supporting open formats like Iceberg, blurring the lake/warehouse line.

## File and table formats

- **Apache Parquet.** The foundational **columnar** file format of the Hadoop/Spark ecosystem. Storing similar column values together makes data highly compressible and fast to scan for the write-once/read-many query patterns typical of analytics. The basis for the major table formats. (Gopalan et al.)
- **ORC (Optimized Row Columnar).** Another open columnar file format used in lakehouse storage layers. (Lad)
- **Avro.** A row-oriented serialization format common in streaming/ingestion pipelines (e.g., Flume sources/sinks) where whole records are written and schemas evolve.
- **Open table formats** add a metadata layer over Parquet to give a logical *table* (rows/columns) with warehouse-like guarantees - ACID-ish transactions, metadata handling, schema enforcement and evolution - on unstructured object storage. A lakehouse requires one of these *plus* a compute engine that understands it:
  - **Delta Lake** (originated at Databricks). Optimized for highly performant SQL on the lake; uses its metadata for intelligent data skipping; brings ACID transactions to Spark on Azure Blob/ADLS via DBFS. (Gopalan et al.; Lad)
  - **Apache Hudi** (originated at Uber). Designed for incremental updates with fast columnar query performance.
  - **Apache Iceberg** (originated at Netflix). Designed to support refreshing/updating datasets on append-only stores (e.g., S3) and to be read by many engines (Spark, Trino/PrestoSQL, Hive, Flink).

### ACID properties (why table formats matter)

Warehouses guarantee ACID transactions to protect high-value data: **Atomicity** (the whole transaction succeeds as a unit), **Consistency** (only valid data is written; otherwise rollback), **Isolation** (concurrent transactions do not affect each other), **Durability** (committed data survives failures). Open table formats bring these guarantees to the lake, which is what makes the lakehouse viable.

## Governance and cataloging

- **Purpose of governance.** To build trust in data - in how it is collected, analyzed, published, and used. Governance is the collection of technologies, tools, and processes ensuring data is secure, accessible, usable, and compliant (GDPR, CCPA). (Gopalan et al., Chapter 3)
- **Actors.** *Data officers* set requirements and run audits; *data engineers* implement the architecture, manage identities, track lineage, and provide audit evidence; *data producers* bring data in and care about its quality and access; *data consumers* use the data/insights.
- **Data classification.** Tag data with infotypes (e.g., zip code, first name) grouped into data classes (PII, financial), then apply policies (e.g., PII collected only with consent). Cloud services (Amazon Macie) automate classification with ML.
- **Data catalog.** A system that stores metadata and publishes it for discovery - consumers search by table name or key fields ("show me all employee tables"). One catalog can span the lake, warehouse, and other stores. The foundation for data sharing.
- **Data sharing.** An emerging governance capability: datasets in a lake or warehouse shared with audiences inside or outside the organization, optionally monetized, made easier by the catalog.
- **Ringed access management.** Inner core: storage-level security on the data itself; next: access to compute engines (ETL, warehouse, dashboards); outer: cloud/network boundaries (e.g., regional data-residency restrictions).
- **Data quality and observability.** Versioning, time travel, validation, and governance policies improve quality; automated quality checks catch anomalies on ingest. (Lad)

## On Azure specifically

- **Azure Data Lake Storage Gen2 (ADLS).** Azure Storage with a hierarchical namespace over blob storage - a single platform for ingestion, processing, and visualization; organized by storage accounts, containers, directories, and files. The hyper-scale repository for big data on Azure. (Gopalan et al.; Lad)
- **Azure Databricks.** Apache Spark-based analytics platform tuned for Azure, with interactive workspaces; builds Delta Lake tables on the Databricks File System (DBFS); the engine for batch layers and unified Kappa-style processing. (Lad)
- **Azure Synapse Analytics.** Unified analytics platform offering provisioned or serverless SQL and Spark, integrated with Azure ML, Cognitive Services, and Power BI; can serve as a warehouse serving layer. (Gopalan et al.; Lad)
- **Data pipeline orchestration.** Azure Data Factory (or Apache Airflow) automates the data lifecycle - ingestion, processing, consumption, ML - reducing manual errors, speeding decisions, preventing data silos. (Lad)
- **Lakehouse on Azure.** ADLS Gen2 as storage + Delta Lake (open format) + Databricks/Synapse compute; Parquet/ORC in the storage layer; a unified metadata catalog for ACID, indexing, and governance. (Lad)

## Patterns

- **Medallion zones (Raw/Enriched/Curated + Sandbox)** - Intent: organize the lake by processing stage and value density to control access, retention, and quality. When to use: any data lake. Trade-offs: clear lifecycle and governance hooks vs. discipline to keep zones clean and avoid a swamp.
- **ELT (extract, load, transform)** - Intent: load raw data into cheap storage first and transform later, preserving the original signal. When to use: data lakes and lakehouses. Trade-offs: future-proofing and flexibility vs. raw zone needing management to avoid sprawl.
- **Lakehouse (open table format + engine)** - Intent: run BI/SQL and data science on one lake with ACID guarantees via Delta/Iceberg/Hudi. When to use: to eliminate the warehouse copy and unify teams. Trade-offs: low cost, no silos vs. careful format/engine selection and a less out-of-the-box path than a warehouse.
- **Modern data warehouse (lake + warehouse)** - Intent: cheap lake storage and ML alongside a warehouse for familiar BI. When to use: organizations modernizing while keeping SQL/BI investments. Trade-offs: familiar tooling vs. two infrastructures, data copies, and an engineer-dependent critical path.
- **Disaggregated compute and storage** - Intent: store once, query with many stateless engines in place. When to use: cloud analytics at scale. Trade-offs: independent scaling and low cost vs. the architect must choose the right format/engine for each query pattern.

## Principles & best practices

- Store raw data in its native form (ELT) - storage is cheap and lost signal is expensive to recover.
- Organize data into zones aligned with usage to control access, retention, and quality.
- Pick the **right file/table format** for the query pattern: columnar Parquet (and Delta/Iceberg/Hudi on top) for analytics; align format and compute engine so the engine can exploit the format's metadata.
- A lakehouse requires *both* an open table format *and* a compatible compute engine - neither alone is enough.
- Design for governance and security from day one (classification, catalog, access rings); do not retrofit.
- Plan for 10x growth: design organization, security, and governance for many users even when you start with one.
- Triangulate architecture choices against the actual business drivers - cost, new scenarios, or a time-bound deadline.

## Anti-patterns & pitfalls

- **Data swamp.** Unmanaged growth and poor organization turn the lake into a swamp where insights are a needle in a haystack. Caused by absent governance, classification, and cataloging.
- **Ignoring data format.** Treating format as a given (as in warehouses) wastes the lake's performance and cost advantages; the architect must choose deliberately.
- **Engineer bottleneck in the modern data warehouse.** BI users and data scientists depend on engineers to move/transform data between lake and warehouse, creating a critical path and duplicate copies.
- **Lakehouse without proper design.** Picking the wrong format/engine combination yields poor performance; the optimized path is not free as it is in a warehouse.
- **Open access by default.** Assuming only engineers will ever touch the lake and skipping access controls leads to accidental data deletes as usage grows.

## Decision guidance

Gopalan et al. compare the four architectures across total cost, flexibility, development complexity, ecosystem maturity, and required organizational maturity:

- **Cloud data warehouse** - high cost, low flexibility (BI/SQL optimized), low complexity, mature for SQL, low org maturity needed. Choose for pure, well-defined BI on structured data with a small team.
- **Modern data warehouse** - medium cost, medium flexibility, medium complexity/maturity. Choose when modernizing while preserving SQL/BI and adding data science.
- **Data lakehouse** - low cost, high flexibility, medium-to-high complexity/maturity. Choose to unify BI and data science cheaply with no silos, accepting that the architecture is newer and needs thoughtful design.
- **Data mesh** - medium cost, high flexibility, high complexity, low tooling maturity, high org maturity. Choose for large, distributed organizations needing to scale past a central bottleneck (see `data-mesh.md`).

Start by **knowing your customers** (BI analysts vs. data scientists) and **knowing your business drivers** (cost reduction, new scenarios, or a deadline). Consider growth and future scenarios so the first design does not paint you into a corner. Hybrid approaches are common - a warehouse-centric organization with a lakehouse innovation pocket, or a lakehouse-standard organization with legacy teams still on old infrastructure. The honest default answer to "cheapest or fastest?" is "it depends" - the lake's low cost relies on the platform team running it in an optimized way.

## Source attribution

- Variety of data, the four building blocks, disaggregated compute/storage, MapReduce/Hadoop/Spark/warehouses, the three architectures (modern warehouse, lakehouse, mesh) with benefits/challenges and the comparison table, ELT vs. ETL, medallion zones, Parquet and the Delta/Iceberg/Hudi table formats with their origins and ACID rationale, governance actors, classification, catalog, sharing, and ringed access management, and the "know your customers/business drivers" decision guidance: **Gopalan et al. (2023)**, Chapters 1–4.
- Enterprise data lakes and lakehouses, the four lakehouse layers, ORC/Parquet in the storage layer, schema drift, ACID in the lakehouse, data pipeline orchestration (Azure Data Factory/Airflow), and the Azure service specifics (ADLS Gen2, Databricks + Delta Lake on DBFS, Synapse): **Lad (2023)**, Chapters 3–4.

## Recent developments (2024–2026)

- **Table-format "war" ended in convergence on interoperability.** Databricks acquired Tabular (the company founded by Iceberg's original creators) in June 2024, and the two leading formats moved toward reading each other rather than competing. Delta Lake UniForm lets a single Delta table expose Iceberg-compatible (and Hudi) metadata so Snowflake, Trino, BigQuery, and others can read it; the emerging principle is "write once, read anywhere." (Capital One Tech, "Lakehouse Format Convergence," 2025, https://www.capitalone.com/tech/cloud/lakehouse-format-convergence-delta-lake-iceberg/; The New Stack, "Snowflake, Databricks and the Fight for Apache Iceberg Tables," 2024, https://thenewstack.io/snowflake-databricks-and-the-fight-for-apache-iceberg-tables/)
- **Iceberg v3 and a shared metadata roadmap.** Apache Iceberg v3 entered public preview on Databricks (deletion vectors, row lineage, new types), and Databricks has stated that future Iceberg v4 and Delta 5.0 will converge on a shared metadata structure. (Databricks Blog, "The Next Era of the Open Lakehouse: Apache Iceberg v3," 2025, https://www.databricks.com/blog/next-era-open-lakehouse-apache-icebergtm-v3-public-preview-databricks)
- **The competition shifted from format to catalog.** Snowflake open-sourced Polaris (an Iceberg REST Catalog implementation, later donated to Apache) and Databricks pushed Unity Catalog with managed/foreign Iceberg support; Polaris added Delta Lake support too. The catalog now governs who can read which tables across engines. (Snowflake Engineering Blog, "Apache Polaris Supports Iceberg and Now Delta Lake," 2025, https://www.snowflake.com/en/engineering-blog/apache-polaris-supports-iceberg-delta-lake/)
- **Iceberg became the de facto open standard for multi-engine lakehouses** in 2025, chosen to avoid single-vendor lock-in across streaming, lakehouse, and AI workloads, while Delta stays Spark-native, Hudi leads CDC/streaming updates, and Paimon targets unified batch-stream. (datalakehousehub.com, "2025 & 2026 Ultimate Guide to the Data Lakehouse," 2025, https://datalakehousehub.com/blog/2025-09-2026-guide-to-data-lakehouses/)
- **New entrants and a streaming-first default.** DuckLake (DuckDB/MotherDuck teams, 2025) stores all table metadata in a relational SQL database rather than JSON/Avro manifests, and Apache Paimon reached maturity in 2025 as a Flink-native streaming lakehouse format. Streaming-first ingestion, autonomous table optimization, and catalog-driven governance are now treated as lakehouse baselines. (Alex Merced, "The Ultimate Guide to Open Table Formats," 2025, https://amdatalakehouse.substack.com/p/the-ultimate-guide-to-open-table)
