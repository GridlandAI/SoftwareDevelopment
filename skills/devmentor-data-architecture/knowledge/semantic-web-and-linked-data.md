# Semantic Web and Linked Data

**Domain:** 08 - Data Architecture and Engineering
**Primary sources:** Linked Data: Structured Data on the Web (Wood, Zaidman, Ruth & Hausenblas, 2023); Data Science with Semantic Technologies (Patel & Debnath, eds., 2023); Building Knowledge Graphs: A Practitioner's Guide (Barrasa & Webber, 2023)

## Overview

Linked Data is a set of best practices for publishing and connecting structured data on the Web using W3C international standards. It turns the Web from a web of documents into a *Web of Data* (the Semantic Web), where developers can query data from multiple sources at once and combine it on the fly - something difficult or impossible with traditional, siloed data-management technologies. The core idea is simple: instead of only linking to files that contain data, you publish data that you can link *into*, using the same primitives that already power the Web (HTTP, URIs, hyperlinks).

For the SDLC this matters because so much organizational value is locked inside proprietary containers - relational databases, spreadsheets, documents - that do not recombine easily, creating information silos. Linked Data frees structured data from those containers, makes it self-documenting (you can resolve a term on the Web to learn what it means), and enables *cooperation without coordination*: publishers expose data for one purpose and consumers reuse it for others they never anticipated. The BBC's Programmes, Music, and Wildlife Finder sites are a canonical example, built by collecting and curating Linked Data from DBpedia, MusicBrainz, and the World Wildlife Fund. Linked Data techniques apply equally to public Web data and to private data behind enterprise firewalls.

## Core concepts

**The four Linked Data principles (Tim Berners-Lee).** Linked Data builds on RDF and is distinguished from raw RDF by four rules:
1. Use URIs as names for things.
2. Use HTTP URIs so that people can look up those names.
3. When someone looks up a URI, provide useful information using the standards (RDF, SPARQL).
4. Include links to other URIs, so people can discover more things.

**The 5-Star scheme.** A cumulative scoring system for open data quality: (1) available on the Web in any format; (2) machine-readable structured data; (3) in a non-proprietary format (for example CSV); (4) published using open W3C standards; (5) all of the above plus links to other people's data. Five-Star Linked Data is the goal, and the move from 4 to 5 stars is precisely "include links to other URIs."

**URI (Uniform Resource Identifier).** A universally unique name for a thing - concrete (a book, a person, a gene) or abstract (love, war, a row in a CSV). All URLs are URIs; a URL is a URI that resolves on the Web. Guidelines for minting good URIs: name things with URIs most of the time; use a DNS domain you control (minting in someone else's domain is rude and erodes trust); use natural keys (human-readable, for example `/baked_goods/bread/rye-12`); keep URIs neutral to implementation details (avoid `.aspx` and similar); and use fragment identifiers (`#...`) with caution because the fragment is processed client-side and never reaches the server. Modern RDF uses IRIs (Internationalized Resource Identifiers); URI and IRI are treated as effectively synonymous.

**RDF (Resource Description Framework).** The data model for Linked Data. RDF is not a single data format; it defines a simple way of expressing relationships between arbitrary data elements that can be serialized in several syntaxes. A single RDF statement (a *triple*) describes two things and a relationship between them - subject, predicate, object. Subjects and predicates are named with URIs; objects can be URIs or literals (strings, numbers, dates, optionally typed and language-tagged). Literals cannot subsequently be used as subjects, which is why Linked Data favors URIs in the object position - they keep the data extensible. Triples connect to form graphs (not just hierarchies), and graphs can be assembled from many sources because they share a common model.

**Vocabularies and namespaces.** RDF vocabularies supply schema information, analogous to a relational schema. Long URIs are abbreviated with prefixes mapped to namespace URIs - for example `dc:` for Dublin Core (`http://purl.org/dc/elements/1.1/`), `foaf:` for Friend of a Friend (`http://xmlns.com/foaf/0.1/`), `rdf:`, `rdfs:` (RDF Schema), and `vcard:`. So `dc:creator` expands to `http://purl.org/dc/elements/1.1/creator`. The shorthand `a` is equivalent to `rdf:type`, stating that a resource is an instance of a class. `rdfs:seeAlso` links to related external resources.

**RDFa.** RDF in Attributes - a way of encoding RDF meaning inside HTML attributes that are invisible in the browser but machine-readable. It powers Google rich snippets (reported 15–30% click-through uplift for Best Buy) and the Facebook Like button, connecting external pages into a social graph.

**SPARQL (SPARQL Protocol and RDF Query Language).** The query language for RDF and therefore for Linked Data - SPARQL is to RDF what SQL is to a relational database. It can query local RDF files, RDF files on the Web, and SPARQL endpoints, and can dynamically build a large virtual RDF graph from multiple distributed sources. By convention, datasets in the Linked Open Data cloud expose an endpoint at the `/sparql` path. Used over HTTP it leverages the SPARQL Protocol: queries are URL-encoded into a `query` parameter on an HTTP GET or POST.

**RDFS and OWL (ontology languages).** RDF Schema (RDFS) provides basic class and property vocabulary (`rdfs:Class`, `rdfs:subClassOf`, `rdfs:domain`, `rdfs:range`, `rdfs:seeAlso`, `rdfs:comment`). The Web Ontology Language (OWL) adds richer expressivity for ontologies - class hierarchies, property characteristics, and constraints - supporting automated reasoning that can derive new facts within computational limits. SKOS supports taxonomical classification schemes. (See knowledge-graphs.md for taxonomies/ontologies in the property-graph context.)

## Frameworks and models

**The Semantic Web stack.** Layered standards built on the Web's foundations: URIs/IRIs and HTTP at the base; RDF as the data model; RDF serialization syntaxes; RDFS and OWL for schema and ontology; SPARQL for querying; and Linked Data principles as the publishing discipline that ties it together. RDFa and the family of RDF standards (sometimes written "RDF*") sit alongside as embedding and extension mechanisms.

**RDF serialization formats.** RDF's first serialization (RDF/XML) gave RDF an early reputation for complexity; modern formats are far simpler. The four most common with Linked Data include Turtle (terse, human-friendly, prefix-based), RDF/XML, N-Triples, and RDFa (embedded in HTML). The same triples can be expressed in any of these; choice is about readability and tooling, and content negotiation lets a server return the format a client requests.

**SPARQL query types.** Five forms cover most needs:
- `SELECT` - returns tabular bindings, like SQL; supports subqueries, `UNION`, `MINUS`, `OPTIONAL` (a left join making patterns non-mandatory), `ORDER BY`, `LIMIT`, `OFFSET`, `GROUP BY`, `HAVING`, `FILTER` (including `regex` and `FILTER NOT EXISTS`), and aggregates (`COUNT`, `SUM`, `AVG`, `MIN`, `MAX`).
- `ASK` - returns a Boolean indicating whether a pattern would match; fast for validating large queries before running them.
- `DESCRIBE` - returns an RDF graph describing a named resource.
- `CONSTRUCT` - builds an arbitrary new RDF graph from `WHERE`-clause bindings (for example extract only English-language statements about a resource).
- `SPARQL UPDATE` (1.1) - add, remove, or modify data via endpoints.

A query is structured as `PREFIX` declarations, `SELECT`, `FROM` (the dataset), `WHERE { ... }` containing *triple patterns* (RDF-statement-shaped patterns with `?variable` placeholders; `?s ?p ?o` matches every statement), and solution modifiers.

**FOAF and friend-of-a-friend graphs.** The FOAF vocabulary models people and their relationships (`foaf:name`, `foaf:knows`, `foaf:based_near`). It is a worked example for creating Linked Data and for distributed SPARQL queries that follow `rdfs:seeAlso` links across documents.

## Patterns

**Resolvable HTTP URIs as identifiers** - *Intent:* name every entity with a dereferenceable HTTP URI so that looking it up returns useful RDF. *When to use:* any data you intend to publish, link, or reuse on the Web. *Trade-offs:* requires owning a stable domain and a publishing endpoint; URIs become a long-term commitment (use neutral, natural-key URIs to keep them durable).

**Link to other people's data (5-Star)** - *Intent:* include outbound URIs to external datasets so consumers can discover more. *When to use:* whenever you publish, to maximize reuse and serendipitous combination. *Trade-offs:* external links can break (404) or drift in meaning; mitigate by caching remote data locally and recording where/when term definitions were valid.

**Cache remote Linked Data locally** - *Intent:* treat a local copy of remote data as a cache rather than querying distributed sources live in production. *When to use:* production services needing reliability and efficiency. *Trade-offs:* data may be stale until refreshed, but you gain resilience against remote outages - the same trade made by the Web itself versus early hypertext.

**Curate at the source** - *Intent:* when consuming third-party Linked Data, fix errors upstream (as the BBC edits Wikipedia) so the shared knowledge improves for everyone. *When to use:* when you have write access and the upstream source is authoritative. *Trade-offs:* depends on community/governance permissions and changes data you do not own.

**CONSTRUCT for graph transformation** - *Intent:* reshape or filter source RDF into a new graph tailored to a consumer. *When to use:* building derived datasets (for example language-filtered subsets) or normalizing across sources. *Trade-offs:* information not connected to the queried resource or outside the `WHERE` clause is lost.

## Principles and best practices

- **Use URIs to name things, HTTP URIs so they resolve, and provide useful information when resolved.** The first three Linked Data principles, in order.
- **Always aim for 5-Star Linked Data.** Anything less leaves work for consumers (conversion, link-building, cleansing).
- **Mint URIs only in domains you control, with natural keys and neutral paths.** This is part of the social contract that makes data trustworthy and durable.
- **Prefer URIs over literals in object position when you may say more about the thing.** Literals are dead ends; URIs keep the graph extensible.
- **Abbreviate with prefixes for readability.** Long URIs are unavoidable but namespaces make RDF and SPARQL legible.
- **Validate large queries with ASK before running expensive SELECTs.** Cheap to test whether any results would return.
- **Record provenance and term validity.** Because schema terms are URIs, you can migrate to a new URI when meaning changes and note where/when a definition held.
- **Self-documenting data is a feature - resolve terms to understand them.** Exploit it for sharing across teams and organizations.

## Anti-patterns and pitfalls

- **Treating Linked Data as a silver bullet.** It does not fix data quality, prevent service failures, improve distributed-query efficiency, or stop schema terms from changing meaning. Dirty data becomes dirty Linked Data instantly.
- **Querying distributed sources live in production.** Reliability and efficiency usually demand local caching instead.
- **Minting URIs in domains you do not own.** Considered rude and undermines trust in your data.
- **Exposing implementation details in URIs.** Non-neutral URIs (revealing the server technology) break when infrastructure changes, invalidating bookmarks and downstream RDF.
- **Careless fragment identifiers.** A `#fragment` is processed client-side and never sent to the server; misuse (for example `cars#porsche`) can cause the server to return everything (all cars).
- **Very long literal objects.** Most RDF stores are not optimized for them; link to a page instead of stuffing large text into a literal.
- **Judging RDF by RDF/XML.** RDF's early complexity reputation came from one verbose serialization; modern syntaxes (Turtle) are simple.

## Decision guidance

- **Is Linked Data the right fit?** Use it when you need to integrate or publish structured data for reuse across teams, organizations, or the public Web, where self-description and standards-based linking pay off. For purely internal, performance-critical, application-specific graphs, a labeled property graph may be a better implementation (see knowledge-graphs.md).
- **Public vs. private.** The same techniques work behind a firewall; keep sensitive data (for example personal financial records) in protected containers while still using common formats to move it.
- **Choosing a serialization.** Use Turtle for human-authored or human-read RDF; use RDFa to embed structured data in HTML for search engines and social platforms; use N-Triples for line-based streaming/processing. Let content negotiation serve the right format per client.
- **RDFS vs. OWL.** Use RDFS for lightweight class/property vocabularies and basic inference; reach for OWL when you need rich ontology constructs and automated reasoning, accepting higher modeling complexity and potentially expensive (even intractable) reasoning.
- **SPARQL query type.** `SELECT` for tabular results, `ASK` to test existence cheaply, `DESCRIBE` for a quick "what is known about X," `CONSTRUCT` to produce a new graph, `UPDATE` to modify endpoint data.
- **Live federation vs. materialized copy.** Federate read-only across endpoints for freshness and breadth; materialize/cache when you need production reliability and speed.

## Source attribution

- Linked Data definition and best practices, the four Linked Data principles, the 5-Star scheme, URI minting guidelines (natural keys, neutral URIs, fragment-identifier caution), the RDF data model (triples, literals vs. URIs, vocabularies, namespaces/prefixes), RDFa and its use in Google rich snippets and Facebook Like, the BBC case study and "cooperation without coordination," DBpedia, SPARQL overview and query types (`SELECT`, `ASK`, `DESCRIBE`, `CONSTRUCT`, `UPDATE`, `OPTIONAL`, `FILTER`, aggregates), the SPARQL Protocol, FOAF, RDF serialization formats, what Linked Data will and will not do, and caching remote data: *Linked Data: Structured Data on the Web* (Wood, Zaidman, Ruth & Hausenblas, 2023), chapters 1–11 and appendices.
- The intersection of the Semantic Web, semantic web services (WSDL, UDDI, BPEL), and machine learning, and the role of OWL/RDF/SKOS in making data machine-readable: *Data Science with Semantic Technologies* (Patel & Debnath, eds., 2023), ch. 1 and ch. 5 "Enterprise Application Development Using Semantic Web Languages."
- RDF as a data-exchange/serialization model versus a storage/query mandate, and the relationship between the Semantic Web stack and property-graph knowledge graphs: *Building Knowledge Graphs* (Barrasa & Webber, 2023), ch. 2 "Data Exchange and RDF."

## Recent developments (2024–2026)

- **A knowledge-graph and Semantic-Web revival, driven by LLM grounding.** Commentators dubbed 2025 "the year of the knowledge graph": RDF/OWL ontologies and SPARQL are increasingly used as a "reliable factual basis" to ground and verify LLM outputs and reduce hallucination, giving the W3C stack in this file renewed practical relevance. (Semantic Arts, "The Year of the Knowledge Graph (2025)," 2025, https://www.semanticarts.com/the-year-of-the-knowledge-graph-2025/)
- **Major vendors invested in RDF/OWL infrastructure.** SAP's 2025 release brought RDF and OWL to the forefront for enterprise customers, and Samsung acquired triplestore vendor RDFox to launch an enterprise knowledge-graph program, signaling that standards-based (rather than only property-graph) semantics are a strategic enterprise layer. (Semantic Arts, 2025, link above)
- **SPARQL repositioned as a strategic enterprise query enabler.** As enterprises deploy large metadata graphs, regulatory ontologies, and enterprise knowledge graphs, SPARQL is described as a key tool for extracting insight from the graph-centric data layer, beyond its original public-Web framing in this file. (TopQuadrant, "Mastering SPARQL," 2025, https://www.topquadrant.com/resources/resources-mastering-sparql/)
- **RDF standards anchored interoperability use cases.** New work applies RDF standards and graph databases to interoperable knowledge graphs (e.g., localized supply chains, industrial digital twins / Asset Administration Shells), reinforcing the "cooperation without coordination" and semantic-interoperability value this file emphasizes. (MDPI, "Interoperable Knowledge Graphs for Localized Supply Chains," 2025, https://www.mdpi.com/2305-6290/9/4/144)
- **GraphRAG and hybrid retrieval renewed interest in graph-based semantics for AI.** Microsoft's GraphRAG (2024) and the move to hybrid vector + graph retrieval in 2025-2026 brought graph-structured knowledge back into mainstream AI pipelines, complementing the property-graph and RDF approaches contrasted in this file. (Microsoft Research, "GraphRAG," 2024, https://www.microsoft.com/en-us/research/blog/graphrag-unlocking-llm-discovery-on-narrative-private-data/)
