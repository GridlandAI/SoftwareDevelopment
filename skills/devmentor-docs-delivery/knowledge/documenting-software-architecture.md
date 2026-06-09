# Documenting Software Architecture

**Domain:** 15 - Documentation and Delivery Practices
**Primary sources:** Documenting Software Architectures: Views and Beyond, 2nd ed. (Clements, Bachmann, Bass, Garlan, Ivers, Little, Merson, Nord, Stafford, 2010)

## Overview
Architecture documentation is the durable record of the most important design
decisions about a system: the structures that carry its quality attributes, the
interfaces and behavior that let parts be built independently, and the reasoning
that explains why the design is as it is. The Views and Beyond (V&B) method,
developed at the Software Engineering Institute, gives a disciplined way to
produce this record. Its central insight is that no single diagram can capture an
architecture; you document a system as a set of *views*, each showing one kind of
structure to one set of stakeholders, plus information that ties the views together
("beyond views").

Good architecture documentation pays off across the SDLC. It is the primary
vehicle for communication among stakeholders, the basis for analysis and review,
the contract that lets distributed teams build to the same design, and the external
memory that lets maintainers and future architects evolve a system without
re-deriving (or violating) its original intent. V&B treats documentation not as an
afterthought of design but as a tool that supports the act of designing itself.

## Core concepts

**Structure, view, and viewtype.** A *structure* is the set of elements of one kind
plus the relations among them (Parnas's original observation: software has many
structures). A *view* is the documented representation of a structure, written for
particular stakeholders and concerns. A *viewtype* (also called a category of
styles) groups views that present the same broad kind of structure. V&B defines
three viewtypes:

- **Module viewtype** - implementation units (modules) and their static,
  code-time relations (is-part-of, depends-on, is-a). Answers "how is the system
  structured as a set of code/work units?"
- **Component-and-connector (C&C) viewtype** - runtime elements (components
  with ports) and connectors (with roles) and their interactions. Answers "how
  does the system behave and interact at runtime?"
- **Allocation viewtype** - mappings between software elements and
  non-software environments: hardware, file systems, teams. Answers "how does
  the software relate to its development and execution environment?"

**Style.** A *style* (the book's term for what others call an architecture pattern at
the documentation level) is a specialization of element and relation types plus
constraints on their use, within one viewtype. Choosing a style for a view fixes
the vocabulary you document in. Major styles by viewtype:

- Module styles: **decomposition** (is-part-of), **uses** (depends-on-correctness),
  **generalization** (is-a), **layered** (allowed-to-use).
- C&C styles: **pipe-and-filter**, **client-server**, **peer-to-peer**,
  **publish-subscribe**, **shared-data**, **tiered**.
- Allocation styles: **deployment** (software to hardware), **install/implementation**
  (software to file system), **work-assignment** (modules to teams).

**Primary presentation and supporting documentation.** Every view has a
*primary presentation* (usually a diagram, sometimes a table) showing the main
elements and relations, plus *supporting documentation* that makes it precise: the
element catalog, context diagram, variability guide, and rationale. An undocumented
diagram is just an "architecture cartoon."

**Documentation beyond views.** Information that spans views or the whole
package: a documentation roadmap, system overview, the mapping between views,
system-wide rationale, and a directory (index, glossary, acronyms).

## Frameworks & models

### The V&B view template (standard organization for a view)
Use one standard organization for every view so readers always know where to
find things. The book's template:

1. **Primary presentation** - the main diagram or table; show only the elements
   and relations central to the view, with a notation key. Cite the published style
   or pattern if the view derives from one.
2. **Element catalog** - explains everything in (and deliberately omitted from)
   the primary presentation:
   a. *Elements and their properties* (each style suggests properties, e.g.
      "responsibility" for decomposition modules).
   b. *Relations and their properties* (exceptions to what the diagram shows).
   c. *Element interfaces* (see interface template below).
   d. *Element behavior* (see behavior section).
3. **Context diagram** - how the portion of the system shown relates to its
   environment (the things outside its scope).
4. **Variability guide** - how to exercise any built-in variation points
   (options, parameters, configuration).
5. **Rationale** - why this design; justify the choice of style or pattern.

Items 2-5 are the supporting documentation. The same template documents a *view
packet* - the smallest bundle you would hand one stakeholder.

### The "documentation beyond views" template
1. **Documentation roadmap** - scope and summary; how the documentation is
   organized (annotated table of contents); view overview (name, style, element /
   relation / property types, notations); how stakeholders use the documentation
   (a stakeholder-to-view table, with short usage scenarios).
2. **How a view is documented** - the view template you adopted, so readers can
   navigate any view.
3. **System overview** - short prose on function, users, constraints, to give a
   shared mental model.
4. **Mapping between views** - how elements in one view correspond to elements
   in another (many-to-many in general).
5. **Rationale** - system-wide decisions and their reasoning.
6. **Directory** - index, glossary, acronym list.

### The interface documentation template (Chapter 7)
An interface is a boundary across which elements interact. Document the provided
and required resources of an element:

1. **Interface identity** (name; multiple interfaces are distinguished).
2. **Resources** - for each: *syntax* (signature: name, parameter types, return
   type), *semantics* (effect: value assignment, externally visible state changes,
   events/messages, side effects, atomicity, sync/async, usage restrictions, often
   as preconditions/postconditions), and *error handling*.
3. **Data types and constants.**
4. **Error handling** (interface-wide).
5. **Variability** provided by the interface.
6. **Quality-attribute characteristics** the interface guarantees.
7. **Rationale and design issues.**
8. **Usage guide** (interaction protocols too complex for resource-by-resource).

Distinguish *signature* (syntax only), *interface* (signature plus semantics), and
*API* (a collection of signatures). Matching signatures is necessary but not
sufficient for correct integration; the semantics must match too.

### Documenting behavior (Chapter 8)
Structure alone does not show how elements act over time. Behavior documentation
supplements views. Two broad approaches:

- **Trace-oriented notations** describe sequences of activity through the system:
  use cases, UML sequence diagrams, communication diagrams, activity diagrams,
  message sequence charts. Good for "what happens when..." stories.
- **State-oriented notations** describe the full space of behavior: statecharts /
  state machines. Good for showing every reachable state and legal transition.

Document behavior where it aids understanding or analysis - typically for elements
with complex, stateful, or concurrent interactions - not for everything.

### The 4+1 and C4 connections
- **Kruchten's 4+1** (logical, process, development, physical, plus use-case
  "+1") is a fixed view set. V&B subsumes it: a logical view maps to a union of
  module styles (decomposition, uses, generalization) and a C&C style; the process
  view maps to a C&C communicating-processes style; the development/implementation
  view to decomposition/layered/uses styles plus a work-assignment allocation view;
  the physical view to a deployment allocation view; the "+1" use-case view to
  behavior documentation that checks the other views.
- **ISO/IEC 42010 (formerly IEEE 1471-2000)** does not prescribe views; it
  requires you to define *viewpoints* (name, concerns framed, model types and
  notations, rationale) and produce conforming *views*, plus stakeholder/concern
  identification and decision rationale. V&B is 42010-compatible: each style guide
  is an implicit viewpoint; the roadmap captures stakeholders and concerns.
- **C4 model** (not in the book; included for the agent's mental map) is a
  lighter-weight contemporary approach using four nested levels - System Context,
  Containers, Components, Code. In V&B terms, C4's Context is a context diagram,
  Containers and Components are C&C / module views at decreasing granularity, and
  Code maps to the element catalog. C4 trades V&B's rigor and completeness for
  approachability; the two are complementary - use C4's diagram conventions inside
  a V&B-style documentation package.

### Combining views
Because separate views can fragment understanding, V&B allows combining views
when their elements have a close (often one-to-one) correspondence. A *combined
view* overlays two viewtypes' notations in one diagram (e.g. a deployment view
that also shows the C&C components on each node). Combine only when it reduces,
rather than increases, reader confusion; otherwise keep views separate and rely on
the mapping-between-views section.

### Architecture Decision Records (rationale as first-class documentation)
V&B elevates rationale to a documented artifact and provides a decision template
(the lineage of today's ADR practice, drawing on Tyree/Akerman and Kruchten):

1. **Issue** - the design issue being addressed.
2. **Decision** - the position chosen.
3. **Status** - pending / decided / approved (state of the decision, not of
   implementation). Kruchten's richer states: idea, tentative, decided, approved,
   challenged, rejected, obsolesced.
4. **Group** - a category for filtering (e.g. data, integration, presentation).
5. **Assumptions** - environmental and need assumptions behind the decision.
6. **Alternatives** - viable options considered (so reviewers do not ask "did you
   think about...?").
7. **Argument** - why this position was selected (cost, time-to-market, resources).
8. **Implications** - new decisions/requirements/constraints created; renegotiations.
9. **Related decisions** - causal, structural, or temporal links.
10. **Related requirements** - traceability to objectives.
11. **Affected artifacts** - elements and relations the decision touches.
12. **Notes** - issues discussed during the decision process.

Document a decision when it has important or hard-to-undo effects, when the team
spent significant effort evaluating options, when it is complex/confusing, or when
it is unusual (likely to be violated by accident).

## Patterns

- **View-based documentation** - Intent: document an architecture as a set of
  views, each one structure for one set of stakeholders, since no single view
  captures the whole. When to use: any non-trivial system whose architecture must
  be communicated, analyzed, or maintained. Trade-offs: more artifacts to keep
  consistent; in return, each view stays tractable and serves its readers.
- **Standard view template** - Intent: document every view with the same
  organization (primary presentation, element catalog, context diagram,
  variability guide, rationale). When to use: always, once you produce more than
  one view. Trade-offs: upfront agreement on a template; pays back in
  navigability and completeness. *(documenting-software-architecture.md)*
- **Three viewtypes (module / C&C / allocation)** - Intent: cover a system's
  static code structure, its runtime structure, and its mapping to environment.
  When to use: as the menu from which you select the views your stakeholders
  need. Trade-offs: choosing too many views wastes effort; too few leaves
  concerns unaddressed.
- **View packet** - Intent: hand each stakeholder only the slice of a view they
  need, with navigation links to parent/sibling/child packets. When to use: large
  views that would otherwise be one massive, unreadable diagram. Trade-offs:
  fragmentation and link maintenance; avoids overwhelming readers.
- **Context diagram** - Intent: bound the scope of a view by showing what is
  inside versus outside and how they connect. When to use: every view; essential
  for any architecture description. Trade-offs: minimal cost; prevents scope
  confusion.
- **Variability guide** - Intent: document variation points so a product or
  configuration can be tailored without re-reading the whole design. When to use:
  product lines, configurable platforms, plug-in architectures. Trade-offs: extra
  section; indispensable when variation is real.
- **Mapping between views** - Intent: record how elements correspond across views
  so readers see the system as one whole. When to use: whenever two views share
  elements (almost always). Trade-offs: a table or rule to maintain; restores the
  unity that view separation breaks.
- **Architecture Decision Record (ADR)** - Intent: capture each significant
  decision with its issue, alternatives, choice, and consequences as durable
  rationale. When to use: decisions that are costly, contested, unusual, or
  hard to reverse. Trade-offs: discipline to write them as decisions are made;
  pays back as external memory, faster onboarding, and fewer re-litigated debates.
- **Active design review** - Intent: drive an architecture review with questions
  the reviewers must *answer* (exercising the document) rather than passively
  scan. When to use: serious architecture evaluations. Trade-offs: harder to
  author good questions and find expert reviewers; surfaces far more defects.

## Principles & best practices
- **Write for the reader, not the writer.** Organize and word documentation
  around the stakeholders' concerns and how they will look things up.
- **Avoid unnecessary repetition.** Record each fact once; reference it from other
  views (pick the most appropriate view to host shared element definitions).
- **Use a standard organization** for views, interfaces, and decisions, and
  *explain* the organization in the "beyond views" section.
- **Record rationale in the heat of the moment** - it is far cheaper to write a
  decision as you make it than to reconstruct it later.
- **Keep notations precise and keyed.** Always include a legend; cite the source
  of any pattern or style by reference, not just by name.
- **Documentation supports design.** Capture decisions in view packets as the
  design unfolds (e.g. one packet per ADD iteration), so documentation and design
  proceed together.
- **Make information comprehensible before beautiful.** During design, capture
  enough to be understood; polish only once the architecture is stable.
- **Apply the cost/benefit test.** Document a view, interface, or decision when
  the payoff (avoided rework, communication, analysis) exceeds the effort.

## Anti-patterns & pitfalls
- **The architecture cartoon** - a box-and-line diagram with no element catalog,
  no defined notation, no rationale; ambiguous and un-analyzable.
- **One giant diagram** - trying to show everything in a single view instead of
  view packets; nobody can read it.
- **Mixing viewtypes by accident** - showing modules and runtime components in
  one diagram without saying so, so readers cannot tell static from runtime.
- **Undefined notation** - inventing arrows and boxes with no key; readers guess.
- **Rationale as fait accompli** - reconstructing reasons after the fact, omitting
  the alternatives actually considered, so reviewers and successors cannot trust
  or revisit the decision.
- **Over-documenting decisions** - capturing trivial decisions with no viable
  alternatives; dilutes the record and burdens the architect.
- **Stale documentation** - views that no longer match the system; worse than
  none, because it misleads.
- **Passive reviews** - meetings where reviewers nod along; they catch little.

## Decision guidance
- **Choosing views:** start from stakeholders and their concerns, list candidate
  views, combine where elements correspond closely, prioritize by expected payoff,
  and produce them in a value-driven order. Do not produce a view just because a
  viewtype exists; produce it because someone needs it.
- **Module vs C&C vs allocation:** if the concern is about code organization,
  build dependencies, or who-builds-what, use module/work-assignment views; if it
  is about runtime behavior, performance, or concurrency, use C&C views; if it is
  about deployment or environment, use allocation views.
- **Combine or separate:** combine views only when the merge clarifies; otherwise
  keep them apart and document the mapping.
- **How much rationale:** apply the four triggers (important/hard-to-undo effect;
  significant evaluation effort; complex/confusing; unusual). Skip the rest.
- **V&B, 4+1, or C4:** use V&B's machinery for rigor and analyzability; adopt
  4+1 or C4 as the *view set / diagram convention* layered on top when teams want
  a familiar, lighter starting point. They are not mutually exclusive.
- **Documentation depth:** match ceremony to risk and audience - a small,
  co-located team needs less than a regulated, distributed program.

## Source attribution
- The view/viewtype/style framework, the three viewtypes and their styles, the
  view template, view packets, context diagrams, variability guides, combining
  views, documentation beyond views, the roadmap, mapping between views,
  interface and behavior documentation templates, the decision/rationale template,
  active design reviews, and the 4+1 and ISO/IEC 42010 mappings all come from
  *Documenting Software Architectures: Views and Beyond*, 2nd ed. (Clements et al.,
  2010) - Prologue and Chapters 1-11 and the Epilogue. The C4 connection is
  editorial context added for the knowledge base and is not from the book.

## Recent developments (2024-2026)
The V&B method (views/viewtypes/styles, the templates, ADRs, behavior, 42010 alignment) is stable and still the rigorous reference. The 2024-2026 movement is lighter docs-as-code stacks and AI-generated architecture documentation.

- **arc42 + C4 + docs-as-code is the de facto pragmatic stack.** Teams pair the arc42 twelve-section template with C4's four nested diagram levels (System Context, Container, Component, Code), authored as code with Structurizr DSL or PlantUML and rendered via AsciiDoc/Asciidoctor in CI (GitHub Pages, Confluence). In V&B terms these are still views, context diagrams, and element catalogs; the change is toolchain, not theory. (github.com/bitsmuggler, "arc42-c4 documentation example"; dev.to, "Comparing Software Architecture Documentation Models," 2025.)
- **AI-generated architecture documentation becomes a research and tooling frontier.** Approaches like "CIAO (Code In Architecture Out)" use LLMs to derive architecture documentation directly from code, and "RAD-AI" rethinks documentation for AI-augmented ecosystems by extending specific arc42 sections (context, building blocks, runtime, cross-cutting, decisions, quality, risks). These automate first drafts while leaving rationale and judgment to humans. (arXiv 2604.08293, "CIAO," 2026; arXiv 2603.28735, "RAD-AI," 2026.)
- **ADRs remain the standard for capturing decisions and are increasingly AI-assisted.** Lightweight Markdown ADRs (the lineage V&B established) are the recommended pairing with C4 for fast-moving teams; tooling now drafts ADRs from diffs and discussion, though the file's discipline holds: record real alternatives and consequences, not after-the-fact justifications. (dev.to, 2025; mcpmarket.com, "arc42 Architecture Documentation Claude Code Skill," 2025.)
- **Documentation increasingly written for LLM consumption.** Architecture docs are now also read by coding agents, raising the value of clean structure, explicit decision records, and machine-parseable formats so AI assistants can ground changes in the documented design. (arXiv 2603.28735, 2026.)
- **The "architecture cartoon" anti-pattern gains new urgency.** As LLMs can emit plausible but unjustified diagrams, the V&B insistence on element catalogs, keyed notation, and rationale becomes the safeguard distinguishing analyzable documentation from generated decoration.
