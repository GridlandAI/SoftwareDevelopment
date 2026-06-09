# Data Observability and Quality

**Domain:** 08 - Data Architecture and Engineering
**Primary sources:** Data Observability for Data Engineering: Proactive monitoring strategies for reliable data pipelines (Michele Pinto and Sammy El Khammal, Packt, 2024)

## Overview
As companies decentralized their data teams and data became a product exchanged between teams via contracts, data quality shifted from "one problem of many" to "the problem." Pinto and El Khammal frame the discipline around a fundamental information bias between data producers (who build pipelines but are not domain experts and work in a near black box) and data consumers (who depend on the output but do not control the supply chain). Data quality is the measurement of how fit data is for its consumers; data observability is the practice of monitoring that quality from inside the running process rather than inspecting only the final dataset. The promise is proactive: catch small issues before they become catastrophes, reduce both the time to detect and the time to resolve incidents, and rebuild the trust that data incidents destroy.

The book's central distinction is that a data quality program alone is insufficient. Predefined checks on final datasets miss problems they were never designed to anticipate, run too late, and lack the context to locate a root cause. Observability adds three things on top of quality: context (which pipeline, which columns, which project), synchronization (metrics emitted as the application runs), and continuous validation (checks embedded throughout the data lifecycle). Together these turn opaque black-box pipelines into interpretable systems that report on their own behavior, the same way application logging makes software debuggable. The result is a framework spanning quality dimensions, SLAs/SLOs/SLIs, the observability metrics themselves (schema, lineage, statistics, freshness), monitoring strategies, anomaly detection, and a disciplined incident-management method.

## Core concepts

- **Information bias** - the misalignment between producers' and consumers' expectations of data quality. Producers may leave fields empty to reduce friction; consumers may need exactly those fields. Quality issues only matter when the affected data item is actually used by a consumer's pipeline, so producers must know what consumers do with the data.
- **Data quality** - fitness of data for its consumers, measured along dimensions. Issues range from pipeline-breaking (no report produced) to subtle (a "Hello {firstname}" email that embarrasses the brand).
- **Data observability** - information from inside the process itself; the pipeline reports its own status and data-quality adherence in a timely manner, converting black-box processes into interpretable ones.
- **Producer and consumer** - the two stakeholders a data contract binds. Producers define clear quality targets, enforce them via robust validation, document with lineage and metrics, and collaborate with consumers. Consumers depend on producer output and detect issues too late if they must validate inputs themselves.
- **Event vs notification** - when an issue is detected, the event is the detection of the anomaly; the notification is how that event reaches the right stakeholders. Good events carry maximum context (which source, which application, when, which environment/project).
- **MTTD and MTTR** - mean time to detect (incident occurrence to detection) and mean time to resolve; the headline metrics a data observability program drives down.

## Frameworks & models

### The seven dimensions of data quality
A pragmatic subset of the DAMA body of knowledge:
1. **Accuracy** - data reflects the real-world value it represents (the right value attached to the right key).
2. **Completeness** - all data the consumer needs is present (no missing required columns, no missing values in used fields).
3. **Consistency** - data is represented the same way across the system (same fields recorded for all records; stable types).
4. **Conformity** - data is in the correct format (an email contains an "@"; a ZIP code matches its pattern).
5. **Integrity** - data items keep their relationships; no orphaned/standalone data that cannot be traced or joined.
6. **Timeliness** - data is current and delivered on time (last week's report actually contains last week's data).
7. **Uniqueness** - no duplicates or overlapping records (one order id per order).

### SLA, SLO, SLI
- **SLA (Service Level Agreement)** - a contract between one producer and one consumer establishing the expected quality of a data asset. It creates producer awareness, assigns responsibility, and gives the consumer trust so they can stop double-checking. SLAs are vertical (one-to-one).
- **SLO (Service Level Objective)** - targets the producer sets to satisfy its SLAs. SLOs are transversal: for a given dataset, one set of SLOs is engineered to satisfy all the SLAs that depend on it. (Example SLA: "I need all transactions by 18+ customers in the last two weeks" yields SLOs like "data contains the last two weeks of transactions" and "data contains only 18+ customers.")
- **SLI (Service Level Indicator)** - the concrete measured indicator that validates an SLO (for example a timeliness indicator comparing data timestamp to process timestamp, a volume indicator, a missing-values indicator). Indicators are computed from observability metrics.

### The three principles of data observability
Data observability = Contextual + Synchronous + Continuous validation:
- **Contextual** - observe only the columns/pipeline relevant to a given project, not the whole shared final table, so metrics are not diluted by data the pipeline never touched and issues can be isolated to the responsible project.
- **Synchronous** - metrics are emitted as the application runs, so detection latency approaches zero and errors are caught before they cascade to downstream applications.
- **Continuous validation** - quality checks are embedded throughout the lifecycle, not only at endpoints, so data is validated as it is produced, ensuring known issues do not propagate.

### Data observability within IT observability
Data observability sits alongside application observability (logs, errors, exceptions) and system/infrastructure observability (cluster size, CPU). Many data issues actually originate in the application (out-of-memory, a code change dropping rows, a decommissioned dependency) or the infrastructure (an undersized cluster delaying output), so combining all three deepens investigations. Data observability is the bridge: it carries metadata about datasets (schema, lineage, metrics) into the same monitoring world as application and system telemetry.

## The observability pillars (key components)
The metrics an observable pipeline emits from inside itself:
- **Schema** - the structure of each data source; a stable schema over time is a baseline expectation, and unexpected schema change is a primary anomaly signal.
- **Lineage** - the dependency graph of data flows. The book emphasizes field-level (column) lineage: for each output column, the input columns used to create/update/fill it. A lineage run binds the lineage to a specific execution with timestamps, separating when the application started from when the transformation actually wrote output. Lineage powers impact analysis and root cause analysis.
- **Metrics (data statistics)** - computed per run and linked to the lineage run and schema:
  - **Distribution observations** - min, max, mean, standard deviation, skewness/kurtosis, quantiles.
  - **Categorical stats** - number of categories, percentage per category.
  - **Completeness observations** - number of rows and number of missing/null values per column.
  - **Freshness** - the timestamp of the data itself (often the oldest and most recent row), used to detect staleness.
  - **KPIs** - custom technical or business metrics worth tracking.
- **Volume** - row counts and affected-row counts, used for completeness and skew detection.
- **Freshness/timeliness** - explicit tracking of data age against expectation.

These pillars (schema, lineage, volume, distribution/metrics, freshness) are the observable surface from which SLIs and anomaly rules are built.

## Monitoring, anomaly detection, and alerting

### Synchronous vs asynchronous monitoring
- **Asynchronous monitoring** checks data after a pipeline run (or on a schedule). Simpler, but detection lags and errors can already have propagated.
- **Synchronous monitoring** observes data while the application runs, aligning metrics with the produced output, slashing detection time toward zero and preventing error cascades and the re-execution of dependent pipelines on bad data.

### Anomaly detection
Anomalies are inferred from observability metrics in three escalating ways:
- **Simple indicator (deterministic) checks** - a single metric against a rule (null rows must be 0; ages within 0–120; schema stable over time; constant interval between runs). Summary deterministic rules: schema stable; null rows not more than 0; lineage creates the same number of columns over time; run interval constant.
- **Multiple-indicator (deterministic) checks** - combine metrics (missing values as a percentage of total rows; a bug-fix version must return results consistent with before the update), bridging application observability and data metrics.
- **Time-series analysis** - once enough runs accumulate, patterns emerge. Decompose into **trend** (long-run increase/decrease, not necessarily linear) and **seasonality** (fixed-period effects such as weekly or holiday peaks). Approximate the expected value from trend plus seasonality; large deviation is an anomaly. Methods include Holt-Winters seasonal, exponential smoothing, and (S)ARIMA.

### Alerting
Metrics should never be checked manually after each run; the system should notify the right people automatically when an event occurs. Notifications use observability context to route to exactly the stakeholders with an interest: the producing team (to fix) and affected consumers (to be cautioned that data they are about to use is faulty). Good alerting drives down MTTD and builds confidence in the data.

## Incident response for data pipelines
A disciplined five-step method, each step supported by logs, metrics, and traces:
1. **Detect the issue** - prefer rule/check-based detection during or right after a run over a consumer reporting that "something is fishy." Distinguish the event (anomaly detection) from the notification (sharing it). Detection without observability surfaces errors only when consumers hit them.
2. **Evaluate impact (impact analysis)** - list every stakeholder, application, project, and data source affected, and judge criticality by business impact (a $10M process outranks a $10K one). Downstream lineage enumerates dependent processes; run frequency informs urgency (a daily sales report needs faster fixing than a monthly one). Without lineage this requires deep architecture knowledge and risks overlooking a stakeholder.
3. **Find the root cause (root cause analysis)** - iterate "why" upstream. A detected issue is often caused by another issue further up the lineage; solving an intermediary step is like a painkiller (the pain returns until the source is treated). For each application creating the data, ask "are there anomalies in the input data sources?" If inputs meet all quality standards, the fault is inside that application; if the earliest input is already wrong, the root cause lies upstream of your observability scope. Two root-cause classes: internal application issues (out-of-memory, a code change removing items, a decommissioned dependency) which producers can fix, and external data-source issues (for example purchased data with missing values) which may be unresolvable internally.
4. **Troubleshoot** - fix at the source using contextual metadata (code owner, code location, code version). For external-provider issues, share that context to support the provider's own RCA. Observability avoids the tedious comparison of codebases to find the culprit.
5. **Prevent future issues** - add or refine checks so the same root cause is caught (or pre-emptively discarded) next time, speeding future RCAs.

## Principles & best practices
- Make pipelines observable from the inside: emit schema, lineage, metrics, and freshness as the job runs rather than inspecting only the final table.
- Always attach context to metrics (pipeline, columns, project, environment); uncontextualized indicators are noise.
- Synchronize observation with execution to prevent error cascades and avoid re-running downstream pipelines on bad data.
- Encode quality expectations as SLAs (contracts) and engineer transversal SLOs and measurable SLIs to satisfy them; this aligns producers and consumers and replaces ad hoc trust.
- Automate alerting and route by lineage to the producer who can fix it and the consumers who must be warned; never rely on manual post-run checks.
- Pursue MTTD and MTTR reductions as the program's measurable goals, alongside a falling count and severity of incidents.
- Combine data, application, and system observability; many "data" issues are really memory, code, or infrastructure issues.
- After every incident, convert the root cause into a new or refined check.

## Anti-patterns & pitfalls
- Running data-quality checks only on the final shared table, where metrics are diluted by columns your pipeline never touched and root causes cannot be isolated.
- Asynchronous-only monitoring that detects issues after they have cascaded, forcing expensive re-execution of downstream pipelines.
- Letting consumers be the detection mechanism; by the time they complain, wrong decisions may already have been made and trust is damaged.
- Treating a symptom (an intermediary issue) instead of the upstream root cause, so the problem recurs.
- Producer and consumer never agreeing on quality expectations, leaving information bias unmanaged and SLAs undefined.
- Manually eyeballing metrics after each run instead of automated, context-routed alerting.
- Ignoring the application and infrastructure dimensions when a "data" issue is actually an out-of-memory or undersized-cluster problem.

## Decision guidance
- To measure quality, score the dataset against the seven dimensions, but only for the fields each consumer actually uses; a column missing for one project may be irrelevant for another.
- To formalize trust, write a vertical SLA per producer-consumer relationship, then derive transversal SLOs for the dataset and concrete SLIs from observability metrics.
- To choose monitoring mode, prefer synchronous observation for anything feeding downstream pipelines (to stop cascades); asynchronous/scheduled observation is acceptable only for low-criticality, slow-moving data.
- To detect anomalies, start with deterministic single-indicator rules (cheap, immediate), add multi-indicator rules for ratios and version consistency, and graduate to trend-plus-seasonality time-series models once enough run history exists.
- To prioritize incidents, run impact analysis first (business value and downstream run frequency) before mobilizing the team; do not jump on the smallest problem.
- To find root causes fast, walk lineage upstream asking "are the inputs anomalous?" until you reach a clean input (fault is in that application) or an already-wrong external input (root cause is upstream of your scope).
- To improve over time, drive MTTD/MTTR down and turn each resolved incident into a preventive check.

## Source attribution
- Producer/consumer roles, information bias, and the data-maturity path: Chapter 1 (Pinto and El Khammal, 2024).
- Seven dimensions of data quality and consequences: Chapter 1.
- SLA/SLO/SLI model (vertical SLAs, transversal SLOs, indicators): Chapter 1.
- Observability definition, the three principles (contextual/synchronous/continuous validation), and data observability within IT observability: Chapter 2.
- Schema, lineage (including field-level lineage and lineage runs), and observability metrics/statistics, freshness: Chapter 4.
- Synchronous vs asynchronous monitoring: Chapter 3.
- Incident management method, impact analysis, root cause analysis, troubleshooting, prevention: Chapter 6.
- Anomaly detection (deterministic single/multiple indicators, time-series trend and seasonality, Holt-Winters/exponential smoothing/(S)ARIMA): Chapter 6.
- MTTD/MTTR and program success metrics: Chapters 2 and 9.

## Recent developments (2024–2026)

- **Observability moved from rule-based checks toward AI-powered monitoring.** The 2024-2026 technical response evolved beyond predefined validation toward systems that learn expected data distributions, detect drift, identify outliers, and autonomously surface root causes, an automation of the time-series / anomaly-detection methods in this file. (IBM/Revefi, "Ways AI is Transforming Data Engineering," 2025, https://www.revefi.com/blog/ways-ai-is-transforming-data-engineering; lakeFS, "The State of Data and AI Engineering 2025," 2025, https://lakefs.io/blog/the-state-of-data-ai-engineering-2025/)
- **Data quality became "the" problem because AI consumes it.** Poor data quality is estimated to cost organizations roughly USD 15M per year on average, and the cost compounds as AI systems ingest low-quality data to produce unreliable outputs, sharpening this file's producer/consumer and MTTD/MTTR emphasis. (Binariks, "10 Latest Trends in Data Engineering for 2025-2028," 2025, https://binariks.com/blog/data-engineering-trends/)
- **Observability adoption is accelerating in distributed estates.** Analysts project that around half of organizations with distributed data architectures will adopt sophisticated observability platforms by 2026, up from under 20% in 2024, driven by data mesh and AI/ML pipelines. (Acceldata, "Big Data Trends 2026," 2025, https://www.acceldata.io/blog/top-8-big-data-trends-shaping-2025)
- **Data observability and AI/ML observability are converging.** Tools such as Monte Carlo and WhyLabs now monitor data quality, lineage, and drift together with model performance, reflecting the growing recognition that data quality is inseparable from AI reliability, an extension of this file's "combine data, application, and system observability" principle to the model layer. (SYNQ, "Data Observability Guide 2025," 2025, https://www.synq.io/blog/data-observability-guide; Monte Carlo, "Data Engineer Roadmap," 2025, https://montecarlo.ai/blog-data-engineer-roadmap/)
- **Machine-readable data contracts now carry quality expectations directly.** Modern contracts bundle schema, SLAs, lineage, ownership, and quality rules into one enforceable artifact, operationalizing the SLA/SLO/SLI model described here. (OvalEdge, "Data Contract in Data Mesh," 2025, https://www.ovaledge.com/blog/data-contract-in-data-mesh)
