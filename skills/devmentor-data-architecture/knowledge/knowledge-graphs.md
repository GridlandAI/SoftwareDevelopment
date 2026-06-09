# Knowledge Graphs

**Domain:** 08 - Data Architecture and Engineering
**Primary sources:** Building Knowledge Graphs: A Practitioner's Guide (Barrasa & Webber, 2023); Semantic AI in Knowledge Graphs (Tiwari, Ortiz-Rodriguez, Ben Abbes, Usip & Hantach, eds., 2024); Data Science with Semantic Technologies (Patel & Debnath, eds., 2023); Semantic Computing and AI (Schimanke, Sert & Chou, eds., 2026)

## Overview

A knowledge graph is a purposeful arrangement of data in which information is placed in context and insight is made readily available. Individual records are positioned in an associative network of relationships that provide rich semantic connectivity, so that data originally captured for one use case (for example, fraud detection) can be reused seamlessly for others (for example, product recommendation). The defining characteristic that separates a knowledge graph from a plain graph is an *organizing principle*: an additional layer of structure (property graph schema, taxonomy, or ontology) that lets a human or a software agent reason about the underlying data without having that meaning hidden inside application code.

Knowledge graphs matter across the SDLC because they make data smarter rather than making applications smarter. Intelligence that would otherwise be re-encoded in every consuming application is encoded once, directly into the data, which reduces duplication, simplifies systems, and encourages reuse. For data and software professionals this provides a non-disruptive way to add contextualized understanding alongside existing warehouses and lakes, and it underpins high-value use cases such as fraud detection, recommendations, customer 360, dependency and impact analysis, semantic search, and, increasingly, grounding for machine learning and large language models.

## Core concepts

**Graph (network).** A simple structure of nodes (vertices) connected by relationships (edges) that creates a high-fidelity model of a domain. Graphs here are networks of connected things, not charts or plots. Graph theory dates to Euler's Konigsberg bridges problem; its enduring lesson is that stripping away real-world noise to focus purely on how things connect yields a more valuable logical representation.

**Labeled property graph (LPG) model.** The most popular model for modern graph databases and a common basis for knowledge graphs. Its primitives are:
- **Nodes** representing entities, carrying zero or more **properties** (key-value pairs) and zero or more **labels** that declare the node's role (for example `Customer`, `Product`).
- **Relationships** representing how entities interrelate. Each has a **type** (for example `BOUGHT`, `FOLLOWS`), a **direction** (from a start node to an end node, possibly the same node), and optional properties. Relationships never dangle.

The LPG model is deliberately *schema-ish*, not schemaless or schema-locked. You apply schema-like constraints (existence, uniqueness) only where you need them, leaving the rest flexible to accommodate messy, uneven, incomplete real-world data. Latency of a query is proportional to how much of the graph you traverse, not to the total size of the graph, and a relationship can be traversed in either direction at the same constant cost.

**RDF (Resource Description Framework) graph model.** Represents data as triples of subject-predicate-object (roughly start node, relationship, end node). Historically conflated with knowledge graphs, but Barrasa et al. argue RDF is fundamentally a model for *data exchange* and serialization, not a mandate for storage or querying. RDF triplestores are one valid implementation; the LPG model is another, and the practitioner book favors LPG for its tooling, performance, and community.

**Organizing principle as contract.** The organizing principle is a contract between the graph and its consumers (human and software). With the property graph model, consuming tools can rely on finding labeled nodes connected by typed, directed relationships, and can visualize, transform, and process the data without domain knowledge. Higher-order organizing principles (taxonomies, ontologies) extend this contract to richer reasoning.

**Taxonomy.** A classification scheme that organizes categories in a broader-narrower (generalization-specialization) hierarchy, built from `Category` nodes connected by `SUBCATEGORY_OF` (a.k.a. `NARROWER_THAN`, `SUBCLASS_OF`) relationships. Taxonomies support *x is a kind of y* reasoning, enable substitutability (offer a similar product when one is out of stock), and let standard algorithms compute semantic similarity (path similarity, Leacock-Chodorow, Wu and Palmer). Multiple taxonomies can be layered dynamically over the same data.

**Ontology.** A richer classification scheme than a taxonomy. Like a taxonomy it describes categories and their relationships, but it is not restricted to hierarchy. Ontologies define complex relationship types (`PART_OF`, `COMPATIBLE_WITH`, `DEPENDS_ON`, `UPSELL`), hierarchies of relationships, and relationship characteristics (transitive, symmetric). Ontologies make knowledge *actionable* - enabling cross-cutting reasoning and acting as a semantic bridge between systems. They can be built modularly and layered to reason across domains.

**Standard ontology languages.** RDF Schema (RDFS) and the Web Ontology Language (OWL) for ontologies; SKOS (Simple Knowledge Organization System) for taxonomical classification schemes. Using a standard language gives software support: OWL-based reasoners can derive new facts within computational limits without understanding the domain.

**Where the data lives.** A knowledge graph is agnostic about physical storage. It can be self-contained in a graph database, a federation of graph stores, a layer over a data lake, or a virtualized smart index over multiple external sources - anywhere on the spectrum from fully virtualized to fully materialized.

## Frameworks and models

**Five-tier path of organizing principles (Barrasa & Webber).** Successive layers of organization, each unlocking more sophisticated reasoning:
1. *Plain old graph* - useful, but interpretation is hidden in the consuming code.
2. *Property graph model* - labels, typed/directed relationships, properties; self-describing to a degree; enables generic processing such as visualization.
3. *Taxonomy* - hierarchical broader-narrower reasoning and similarity.
4. *Ontology* - multilevel, non-hierarchical relationships; actionable cross-domain reasoning.
5. *Layered/cross-domain ontologies* - bridge multiple underlying taxonomies and ontologies to reason across the whole business.

**Classes of graph algorithms (graph data science).** Three broad categories: *statistical* (metrics about the graph - counts, degree distribution), *analytical* (surface patterns and latent knowledge), and *machine learning* (use algorithm outputs as features, or evolve the graph itself). Within statistical/analytical work, five families recur:
- **Network propagation** - deep path computations modeling how signals (disease, supply-chain weakness) spread.
- **Influence / centrality** - identify nodes that act as bridges or bottlenecks (betweenness, eigenvector, PageRank, article rank, degree centrality).
- **Community detection** - partition tightly knit groups (Weakly Connected Components, Louvain), used for fraud rings and topic clusters.
- **Similarity** - find repeated patterns for recommendation and deduplication.
- **Link prediction** - compute missing relationships from existing topology (suggested friends, follows).

**Graph data science execution model (Neo4j GDS).** Four phases: read a *projection* (a subgraph, label/relationship subset, or whole graph), load it into a compact in-memory format, run a parallelized algorithm, then stream, write back, or mutate results to enrich the graph. Tight integration with the database minimizes data wrangling. Neo4j GDS is optimized for CPU and RAM and often outperforms GPU solutions because not all graph algorithms reduce to linear algebra.

**Three interactions between NLP and knowledge graphs.** (1) *Natural language as input* - NER and fact extraction populate the graph. (2) *Natural language as output* - generate conversational answers or reports from the graph. (3) *The graph as structured context for NLP tasks* - lexical or conceptual grounding. This framing organizes how language models and knowledge graphs combine.

**From knowledge graphs to knowledge lakes.** As knowledge graphs proliferate from departmental assets to cross-functional and then foundational systems, the deployment pattern evolves into a *knowledge lake*: a general-purpose, contextualized graph that subsumes other knowledge graphs and non-graph data, sitting beside data warehouses and lakes. A data lake ingests volume on the premise that meaning comes later; the knowledge lake supplies that meaning after the fact, becoming the primary lens through which business-meaningful data is consumed.

## Patterns

**Property graph as the default model** - *Intent:* represent a domain with information-rich, self-describing data using nodes, labels, typed/directed relationships, and properties. *When to use:* most operational and analytical knowledge-graph workloads where flexibility, tooling, and performance matter. *Trade-offs:* labels are role tags without inheritance, so type-system semantics must be added via taxonomy/ontology or handled in the application.

**Just-enough semantics** - *Intent:* introduce only the organizing principle your current use cases demand, adding more as needs grow. *When to use:* always, as the default posture for building organizing principles. *Trade-offs:* avoids over-engineering and "boil the ocean" ontology projects, at the cost of revisiting and versioning the model as the business evolves. Pairs naturally with iterative, Agile delivery.

**Adopt a standard ontology vs. build your own** - *Intent:* reuse an established domain ontology (SNOMED CT, FIBO, Schema.org, Dublin Core, Library of Congress Classification) for interoperability, or author a custom one for fit and control. *When to use:* adopt a standard when one exists for your domain or when interoperability or regulatory reporting is required; build your own when no standard fits or you need fine-grained control. *Trade-offs:* standards bring reuse and interoperability but may be a partial fit; custom ontologies fit precisely but cost time and expertise to build and maintain.

**Graph-based entity resolution (identity knowledge graph)** - *Intent:* resolve duplicate or fragmented records into golden master entities by representing candidate records and shared attributes (address, phone) as a graph, then matching and merging. *When to use:* customer 360, know-your-customer, master data management, deduplication. *Trade-offs:* requires data preparation, blocking keys, and matching/community-detection algorithms; quality depends on attribute coverage and disambiguation rules.

**Pattern-detection knowledge graph for fraud** - *Intent:* load tabular transactional data into a graph so fraud rings and first-party fraud surface as structural patterns (shared addresses/phones across supposedly independent customers, abnormal community structure). *When to use:* financial crime, anti-fraud, where rule-based scoring is slow and inflexible. *Trade-offs:* needs ongoing curation and algorithm tuning (for example Louvain communities, WCC); benefits compound because even marginal detection gains translate to large dollar savings.

**Dependency knowledge graph** - *Intent:* model components and their dependencies (including recursive, qualified, temporal, and multi-dependencies) to compute impact propagation, root-cause analysis, and single-point-of-failure analysis. *When to use:* infrastructure, supply chains, IT service management, bills of materials. *Trade-offs:* must validate for cycles (a DAG), aggregate correctness, and redundant configuration; modeling semantics of multidependency (additive vs. redundant) is non-trivial.

**Knowledge graph as context for LLMs / question answering** - *Intent:* extract entity-property-value facts from text into a graph that answers domain questions ("Who is the CEO of Tesla?") rather than returning documents; and translate natural-language questions into structured graph queries. *When to use:* conversational interfaces, question answering, grounding generative systems in curated facts with provenance. *Trade-offs:* fact extraction needs harmonization to avoid relationship proliferation; rule-based NL-to-query (for example spaCy matchers to Cypher) is precise but must be maintained, though it can be driven dynamically from the graph's self-describing schema.

## Principles and best practices

- **Put what you know about your domain into the graph.** If you know something (a label, a constraint, a relationship), encode it; it adds fidelity and improves query performance (labels build indexes the planner uses).
- **Make data self-explanatory rather than relying on an instruction manual.** Encoding the organizing principle into the data avoids reverse-engineering when the original authors leave.
- **Don't boil the ocean.** Resist building an all-encompassing metamodel up front; it is expensive, quickly stale, and usually unnecessary. Embrace just-enough semantics.
- **Knowledge graphs must be performant, flexible, and easy to maintain.** Slow or hard-to-change graphs fall into disuse; fast, current ones empower users.
- **Knowledge graphs are not static.** Use enriches them - encourage interaction so the graph improves in a virtuous cycle.
- **Use `MERGE` to avoid duplicates when enriching.** `CREATE` always inserts; `MERGE` inserts only if the full pattern does not already exist. `DETACH DELETE` cleanly removes a node and its relationships.
- **Choose the data model independently of the wire format.** RDF is a reasonable sharing/exchange format, but your storage and query model should not be dictated by it.
- **Deploy non-disruptively.** Knowledge graphs can sit alongside existing systems, virtualizing or materializing data, without rip-and-replace projects; start as small as a single system and grow.

## Anti-patterns and pitfalls

- **Ontological perfectionism.** Trying to capture the entire language of the business in detail before delivering value - high cost, low certainty of payoff, and the model is out of date by completion.
- **Over-engineering the organizing principle.** Building a complex ontology when a taxonomy or plain property graph would meet current needs.
- **Hiding meaning in application code.** Leaving interpretation of a plain graph encoded in queries and programs, creating fragility and key-person risk.
- **Conflating RDF with knowledge graphs.** Assuming an RDF triplestore is required; this confuses an exchange format with the concept itself.
- **Unharmonized fact extraction.** Letting NLP extraction proliferate inconsistent relationship types without disambiguation input.
- **Tabular thinking for connected problems.** Storing relationship-rich problems (fraud, dependencies) in tables obscures the very structure that holds the insight.
- **Letting a knowledge lake become an ungovernable sprawl.** Without governance and automation, proliferating knowledge graphs degrade into a mess; the lake needs curation to avoid going stale or patchy.

## Decision guidance

- **Pick the organizing principle by intended use, not ambition.** There is no value in rich features without a process or agent that uses them. Start at the lowest tier that solves the current problem (property graph), and climb to taxonomy or ontology only when reasoning needs require it.
- **Property graph vs. RDF.** Favor the labeled property graph for operational knowledge graphs needing flexibility, mature tooling, and performance; consider RDF/SPARQL where standards-based exchange, web publishing, or formal OWL reasoning is a primary requirement (see semantic-web-and-linked-data.md).
- **Standard vs. custom ontology.** Default to a standard if one exists for your domain (interoperability, possibly mandated); build custom only for poor-fit or fine-grained-control situations, ideally starting from a public standard.
- **Algorithm selection.** Sort by the kind of insight needed (statistical, analytical, ML), then by family (propagation, influence, community, similarity, link prediction); when several algorithms fit (for example WCC vs. Louvain), experiment and choose by results in your context.
- **Materialize vs. virtualize.** Materialize when you need performance and a curated, hosted graph; virtualize when the graph should be a smart index over authoritative external sources; hybrids are common.
- **When to introduce a knowledge lake.** Move from many point knowledge graphs to a knowledge-lake architecture when graphs become foundational and cross-functional, but treat the lake as a complement to (not a replacement for) the data lake and warehouse.

## Source attribution

- Definitions of knowledge graphs, organizing principles as contracts, the labeled property graph model, taxonomies vs. ontologies, just-enough semantics, "don't boil the ocean," standard vs. custom ontologies, RDF as an exchange format, Cypher `CREATE`/`MERGE`/`DELETE`, graph databases (Neo4j), graph data science classes and execution phases, NLP-to-graph interactions, fraud/identity/dependency/semantic-search use cases, and the knowledge-lake future direction: *Building Knowledge Graphs: A Practitioner's Guide* (Barrasa & Webber, 2023), chapters 1–14.
- Combining knowledge graphs with machine learning and deep learning, KGs and ontologies, and the reliability/understandability of KG-expressed knowledge: *Data Science with Semantic Technologies* (Patel & Debnath, eds., 2023), ch. 1 "Machine Learning Meets the Semantic Web," ch. 2 "Knowledge Graphs."
- Semantic NLP for knowledge-graph creation, event-centric KGs for crime analysis (Louvain communities, centrality scoring), KGs in healthcare, educational recommenders addressing the cold-start problem, and building KGs from relational databases: *Semantic AI in Knowledge Graphs* (Tiwari et al., eds., 2024).
- Knowledge-graph-based explainable AI and graph computing for financial crime/fraud detection: *Semantic Computing and AI* (Schimanke, Sert & Chou, eds., 2026), ch. 6 and ch. 13.

## Recent developments (2024–2026)

- **GraphRAG made knowledge graphs central to LLM retrieval.** Microsoft Research introduced GraphRAG in 2024: it builds an LLM-generated knowledge graph from a corpus (entity/relationship extraction, Leiden-algorithm community detection, hierarchical summarization) to answer global, "connect-the-dots" questions that vector-only RAG cannot. This operationalizes this file's "knowledge graph as context for LLMs" pattern. (Microsoft Research, "GraphRAG: Unlocking LLM discovery on narrative private data," 2024, https://www.microsoft.com/en-us/research/blog/graphrag-unlocking-llm-discovery-on-narrative-private-data/; IBM, "What is GraphRAG?," 2025, https://www.ibm.com/think/topics/graphrag)
- **Hybrid retrieval became the production-grade architecture.** The most effective 2025-2026 systems combine vector retrieval, knowledge-graph traversal, and structured database lookup; benchmarks show vector-only RAG failing (near 0% accuracy) on schema-bound KPI/forecast queries where optimized GraphRAG reaches 90%+. Microsoft also shipped LazyGraphRAG and surfaced GraphRAG in its Discovery agentic research platform. (Salfati Group, "Graph RAG Guide 2025," 2025, https://salfati.group/topics/graph-rag; Microsoft Research, "Project GraphRAG," 2025, https://www.microsoft.com/en-us/research/project/graphrag/)
- **Knowledge graphs as the antidote to hallucination.** Enterprise pipelines increasingly use KGs and ontologies as a "reliable factual basis" to ground and verify LLM outputs; an ontology-grounded clinical QA system reported 98% accuracy versus 37% for a bare LLM, with hallucinations cut from 63% to under 2%. (MDPI/neurosymbolic studies, 2025; IMO Health, "Grounding clinical AI with knowledge graphs," 2025, https://www.imohealth.com/resources/grounding-clinical-ai-with-knowledge-graphs/)
- **2025 was widely called "the year of the knowledge graph."** Enterprise momentum included SAP foregrounding RDF/OWL in its 2025 release and Samsung acquiring triplestore vendor RDFox to launch an enterprise knowledge graph program, validating this file's "knowledge lake" trajectory. (Semantic Arts, "The Year of the Knowledge Graph (2025)," 2025, https://www.semanticarts.com/the-year-of-the-knowledge-graph-2025/)
- **Automated KG construction from unstructured data improved.** New methods (e.g., GraphMERT, 2025) distill reliable, schema-conformant knowledge graphs from text at scale, advancing the ML-extended-graph pattern while still requiring harmonization. (arXiv, "GraphMERT," 2025, https://arxiv.org/pdf/2510.09580)
