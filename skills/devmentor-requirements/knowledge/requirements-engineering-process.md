# The Requirements Engineering Process

**Domain:** 02 - Requirements & Specifications
**Primary sources:** Software Requirements Essentials (Wiegers & Hokanson, 2023); Software Requirements, 3rd Edition (Wiegers & Beatty, 2013); More About Software Requirements: Thorny Issues and Practical Advice (Wiegers, 2009); Requirements Engineering: Foundation for Software Quality / REFSQ 2011 (Berry & Franch, eds., 2011)

## Overview
Requirements engineering (RE) is the disciplined set of activities a team performs to discover, understand, communicate, agree on, and maintain what a software solution must do and how well it must do it. It is the foundation for everything downstream: if the requirements are wrong, no amount of excellent design, coding, or testing will produce a successful product. Wiegers states this as a "cosmic truth": *if you don't get the requirements right, it doesn't matter how well you execute the rest of the project.*

RE is not a phase that ends before construction starts. It is incremental, iterative, and continues throughout the operational life of the product as change requests arrive. The discipline divides into two broad domains: **requirements development** (eliciting, analyzing, specifying, and validating requirements) and **requirements management** (handling change, versioning, status tracking, and traceability once requirements exist). This file covers the development process and its elicitation techniques; requirements management has its own topic file.

## Core concepts

### The two domains and four subdomains
Wiegers and Hokanson partition RE as follows:

- **Requirements development** - the activities to identify, understand, and communicate requirements knowledge. Subdivided into:
  - **Elicitation** - collecting, discovering, and *inventing* requirements. "Gathering" is a misnomer; requirements rarely sit fully formed in stakeholders' heads. Cosmic Truth #2: *requirements development is a discovery and invention process, not just a collection process.*
  - **Analysis** - assessing requirements for detail, value, interconnections, feasibility, conflicts, and gaps to reach a precise-enough understanding to implement at low risk.
  - **Specification** - representing requirements knowledge in persistent, communicable forms.
  - **Validation** - assessing whether requirements, once implemented, will actually satisfy stakeholder needs.
- **Requirements management** - baselining, version control, status tracking, traceability, and change control (see `requirements-management-and-change.md`).

These four development subdomains are **not a linear, one-pass sequence.** They are interwoven, repeated, and often concurrent. The team iterates on a chunk of requirements until it understands them well enough to build and verify that part at low risk, then moves to the next chunk. (Wiegers & Hokanson, 2023; Wiegers, 2009)

### Who does the work: the business analyst role
The person responsible for requirements has historically been called a requirements analyst, systems analyst, requirements engineer, product manager, or product owner. The term **business analyst (BA)** has largely subsumed these. Critically, "BA" denotes a **role, not a job title** - even a team without an official BA must have someone elicit, analyze, specify, validate, and manage its requirements. Valued BA skills include listening, interviewing/questioning, facilitation, analytical and systems thinking, modeling, writing, flexibility across the abstraction scale, observation, and curiosity. (Wiegers & Hokanson, 2023; Wiegers & Beatty, 2013)

### Recurrent themes (apply throughout the process)
- **Incremental and iterative by necessity.** No one thinks of all requirements up front, and they will not remain unchanged.
- **The goal of all specification is clear, effective communication** - not stylistic purity or standards conformance. Different audiences want different forms and levels of detail.
- **RE is collaborative.** Cosmic Truth #4: *the interests of all stakeholders intersect in the requirements process.* Cosmic Truth #5: *customer involvement is the most critical contributor to software quality.*
- **Change happens** (Cosmic Truth #3). The target is moving; the BA must keep needs understood, recorded, and communicated.
- **Push quality to the front.** Reducing rework is the biggest productivity lever; finding requirement errors before they are coded is far cheaper than fixing them later (100–200x cheaper after deployment, per Davis, cited by Koelsch).
- **Use risk thinking** to decide which practices to apply, when, and how much detail to capture. Outsourced or distributed projects need more precise, more detailed requirements than co-located, highly collaborative teams.

## Frameworks & models

### Foundation practices (do these first)
Wiegers and Hokanson identify five foundation activities that align stakeholders before deep requirements work:

1. **Understand the problem before converging on a solution.** Avoid presuming a presented problem *or* a presented solution is correct. Perform root cause analysis (5 Whys, fishbone/Ishikawa diagram) until the real issue and contributing factors are understood. If presented with a solution, ask "If <solution> is the answer, what was the question?" Capture results in a **problem statement** (Situation, Problem, Implication, Benefit, Vision).
2. **Define business objectives.** Specify *why* the organization is doing the project - business opportunities/objectives, success metrics, and a vision statement (see `requirements-types-and-quality.md` for detail).
3. **Define the solution's boundaries.** Establish "what's in, what's out." Tools: **context diagram** (single system boundary and external entities/data flows) and **ecosystem map** (all systems in the domain and the data flowing between them).
4. **Identify and characterize stakeholders** (below).
5. **Identify empowered decision makers** (below).

### Stakeholder analysis
A **stakeholder** is any individual or group actively involved in, affected by, or able to influence a project. Steps:

- **Cast a wide net.** Use a reusable stakeholder catalog from prior projects, an org chart, and a standard taxonomy (development team, inside the developing org, outside it). Ask: who controls budget/schedule? who articulates objectives? who uses the product directly/indirectly? who imposes legal/compliance/regulatory constraints? whose business processes are affected? who supplies data?
- **Distinguish stakeholders, customers, and users.** Not all stakeholders are customers; not all customers are users. Treat each **user class** as a separate subcategory (users differ by features used, tasks, frequency, location, privilege, experience). Identify **favored user classes** whose interests most closely align with business objectives - their input carries more weight in conflicts.
- **Characterize each group**: who/how many, where, roles, power/influence, interests/fears, benefits sought, information they can supply. Record in **stakeholder profiles**; aggregate into an enterprise stakeholder catalog for reuse.
- **Select representatives.** The **product champion** is a designated, empowered representative of a specific user class who serves as the literal voice of the customer through interviews, workshops, and prototype evaluations. If real users are unavailable, someone must still act as a proxy.
- **RACI matrix** identifies who is Responsible, Accountable, Consulted, Informed for project decisions.

(Wiegers & Hokanson, 2023; Wiegers & Beatty, 2013)

### Empowered decision making
Every project faces a stream of requirements decisions: resolving conflicts within/across user classes, prioritizing, making quality-attribute trade-offs, scoping increments, managing the backlog, changing scope. Identify decision makers **before** the first major decision so issues are resolved at the lowest possible level. Each group should pick a **decision leader** and a **decision rule**:

- **Unanimous vote** - all must agree; strongest commitment, slowest.
- **Consensus** - all can live with the decision; strong buy-in, requires discussion/compromise.
- **Plurality (majority) vote** - best for low-impact decisions with clear options.
- **Decision leader decides** - fastest; soliciting input first improves commitment.
- **Delegation** - leader appoints a knowledgeable delegate; overriding the delegate erodes trust.

No rule is universally correct, but every decision-making group should agree on its rule and then follow it. Decisions related to requirements should use the **business objectives as the "North Star."** Record significant decisions with their rationale, and communicate non-local decisions to all affected parties. (Wiegers & Hokanson, 2023)

### REFSQ research-grounded frameworks
The academic RE community (REFSQ 2011) contributes formal and research-validated approaches that complement the practitioner methods above:

- **Goal-oriented RE**: the **i\* (i-star)** framework models socio-technical systems as actors with goals, soft-goals, tasks, and dependencies; **KAOS** decomposes high-level goals into requirements and assigns responsibilities to agents. Goals give a precise criterion for feature pertinence and a basis for validating and prioritizing alternatives.
- **Problem frames** (Jackson) structure the problem space and influenced industrial methods such as REVEAL.
- **The requirements pyramid**: artifacts sit at abstraction levels by detail and formality; traceability relates artifacts across levels.
- **NLP for RE**: automated detection and measurement of ambiguity in natural-language specifications, and concept extraction from user stories.
- **Creativity techniques** applied to RE to invent (not just collect) requirements for novel socio-technical systems (e.g., air traffic management).

(Berry & Franch, eds., 2011)

## Patterns

- **Root cause analysis before solutioning** - *intent:* ensure the team solves the real problem. *When:* whenever a problem or solution is handed to you. *Trade-offs:* takes time up front; prevents the "project success, product failure" scenario where you build the wrong thing well.
- **Context diagram + ecosystem map** - *intent:* fix and communicate solution scope. *When:* at project start and whenever scope is questioned. *Trade-offs:* models can go stale; high reuse value across projects.
- **Product champion** - *intent:* give each user class a single empowered voice. *When:* large or diverse user populations, limited direct access to real users. *Trade-offs:* a poorly chosen champion can misrepresent the class; far better than design-by-committee or no user voice.
- **Decision rule agreement** - *intent:* resolve requirements conflicts quickly and fairly. *When:* before the first major decision. *Trade-offs:* small upfront overhead; avoids stalled, revisited, and resented decisions.
- **Interview patterns** (REFSQ) - *intent:* structure elicitation interviews to surface tacit knowledge. *When:* one-on-one elicitation. *Trade-offs:* requires interviewer skill.

## Principles & best practices
- Treat elicitation as discovery and invention, drawing requirements forth from many sources: user representatives and other stakeholders, documentation of business processes and current/competing systems, laws/regulations/policies, existing systems, and help-desk/problem records.
- Select elicitation techniques by fit: type of information needed; who has it and where; effort, budget, and time; the team's life cycle; and organizational cultures.
- Ask usage-centric questions ("What do you need to *do* with the solution?") rather than solution-centric ones ("What features do you want?").
- Validate continuously, not just at the end. Validation is interwoven with elicitation, analysis, and specification.
- Keep the business problem and objectives visibly in focus throughout (posters, refresher slides) so decisions stay aligned to value.
- Build reusable assets: stakeholder catalogs, glossaries, business rules, ecosystem maps, security requirements.

### Common elicitation techniques
| Technique | Typical participants | Best for |
|---|---|---|
| Document analysis | BA | Existing systems, processes, regulations |
| Interviews | BA + stakeholders | Deep individual knowledge, tacit needs |
| Facilitated group workshops | BA + stakeholders | Building shared understanding, resolving conflicts |
| Observation (users at work) | BA + users | Tacit process knowledge users cannot articulate |
| Prototyping | BA + users | IKIWISI ("I'll know it when I see it") needs, UI exploration |
| Scenario analysis / use cases | BA + stakeholders | Interactive systems, task flows |
| Process modeling | BA + stakeholders | Business workflows |
| Focus groups, brainstorming, mind mapping | BA + stakeholders | Idea generation, market input |
| Questionnaires and surveys | BA | Large, distributed populations |
| System/user/data interface analysis | BA + stakeholders | Integrations, data exchange |

(Wiegers & Hokanson, 2023; Wiegers & Beatty, 2013)

## Anti-patterns & pitfalls
- **Treating elicitation as a passive collection exercise** ("just write down what they ask for"). The output is a "mishmash of functionality fragments" with no structure - not a specification.
- **Converging on a solution before understanding the problem** - leads to solving the wrong problem (project succeeds, product fails).
- **Solution-centric elicitation questions** - produces unused features and missing capabilities.
- **Treating "the user" as monolithic** - overlooks distinct user classes and their conflicting needs.
- **Overlooking stakeholders** - creates requirements gaps and surprise constraints discovered late and expensively.
- **No agreed decision rule** - decisions stall, get revisited, and breed resentment; the loudest voice wins.
- **Skipping foundation activities** and "going back later when the team runs into problems."
- **One-size-fits-all process** - the right set of practices depends on project nature, size, risk, team experience, and stakeholder access.
- **Confusing sign-off with validation** - signing off is approval; validation gives confidence the requirements are correct.

## Decision guidance
- **How much RE rigor?** Scale by risk. More detail and precision for: complex multi-component or safety-critical systems, outsourced/distributed teams, unfamiliar domains, weak decision processes, language barriers, large projects. Less written detail (but never zero) for: co-located, highly collaborative teams on low-risk, rapidly evolving products with strong domain experience and reusable assets.
- **Which elicitation technique?** Match the information type and source. Tacit, observable process knowledge → observation. Deep individual expertise → interviews. Shared understanding and conflict resolution → workshops. "I'll know it when I see it" UI needs → prototyping. Large distributed populations → surveys.
- **Up-front vs. just-in-time requirements?** Information systems and high-change-cost products warrant more up-front work; rapidly evolving apps benefit from just-in-time, small-chunk requirements; innovative products need concept exploration, prototyping, and market assessment.
- **Select practices deliberately.** This is not a fixed process. Whoever leads requirements should choose, with other leaders, the approaches that add the most value and tailor them.

## Source attribution
- The two-domain / four-subdomain structure, the five foundation practices, stakeholder analysis, decision rules, elicitation technique table, and recurrent themes are from **Software Requirements Essentials** (Wiegers & Hokanson, 2023, Chapters 1–3), building on **Software Requirements, 3rd Edition** (Wiegers & Beatty, 2013).
- The Cosmic Truths, the cost-of-late-requirements argument, and the discovery-vs-collection framing are from **More About Software Requirements** (Wiegers, 2009) and reiterated in the 2023 Essentials book.
- Goal-oriented RE (i\*, KAOS), problem frames, the requirements pyramid, NLP-for-RE, interview patterns, and creativity-in-RE are from **Requirements Engineering: Foundation for Software Quality / REFSQ 2011** (Berry & Franch, eds., 2011).

## Recent developments (2024–2026)
The RE process structure (the two domains, four subdomains, foundation practices, stakeholder analysis, and elicitation techniques) is stable. The significant 2024-2026 development is the rapid arrival of LLMs across every RE activity, which the academic and practitioner communities are now studying and codifying.

- **LLMs across the RE lifecycle.** Systematic literature reviews published in 2024-2026 surveyed dozens of studies applying LLMs to elicitation, analysis, specification, modeling, validation, prioritization, and traceability. The consistent finding: LLMs improve speed and standardization but the quality of generated requirements depends heavily on the inputs and prompts and almost always needs human revision, preserving the BA's analyze-and-validate role rather than replacing it. (Cheng et al., "Generative AI for Requirements Engineering: A Systematic Literature Review," Software: Practice and Experience, Wiley, 2026; arXiv 2409.06741; Arora et al., 2024)
- **Elicitation augmented, not automated.** LLMs are used to draft interview questions, summarize and cluster elicitation transcripts, generate candidate scenarios, and surface gaps, supporting Wiegers and Hokanson's "discovery and invention, not collection" framing by acting as a brainstorming and synthesis partner. Customer involvement (Cosmic Truth #5) remains the dominant quality factor; AI does not substitute for real stakeholder input. (Cheng et al., 2026)
- **AI-assisted ambiguity and quality analysis in tooling.** The REFSQ-era NLP-for-RE thread has matured into production features: requirements platforms now flag ambiguity, passive voice, vagueness, and incompleteness as the analyst writes (Jama Connect Advisor, IBM DOORS Next Requirements Quality Assistant). This operationalizes the analysis subdomain at authoring time. (Jama Software, 2025-2026)
- **Spec-driven development pulls RE into the AI build loop.** GitHub Spec Kit and Amazon Kiro make an explicit requirements artifact the executable source of truth an AI agent builds against (Kiro authors requirements as EARS user stories plus acceptance criteria), giving the "specification" subdomain a new, immediate downstream consumer and raising the value of precise, well-structured requirements. (GitHub Blog, 2025; Kiro, 2025; Martin Fowler, 2025)
- **Human oversight is the recurring caveat.** Across studies and tools, the consensus is that prompt design and human review remain essential; LLM-generated requirements can be plausible but wrong, miss tacit context, or drift from business objectives, so validation against the business "North Star" is more important, not less. (Cheng et al., 2026; arXiv 2507.15157, 2025)
