# Technical Documentation

**Domain:** 15 - Documentation and Delivery Practices
**Primary sources:** Docs for Developers: An Engineer's Field Guide to Technical Writing (Corleissen, Bhatti, Nunez, Lambourne, Schlawack, 2021); Code, Document, Control (Wieteska, 2025)

## Overview
Technical documentation is the writing that helps people understand and use a
software product: the READMEs, tutorials, how-to guides, conceptual explanations,
and API references that surround the code. For developer-facing products it is part
of the product, not an accessory - docs are how users discover what a service does,
how they adopt it, and how they recover when they get stuck. Poor or missing docs
are one of the largest causes of friction in adoption and support load.

Docs for Developers frames documentation as a craft engineers can learn and apply
with the same rigor as code: understand your audience, plan content deliberately,
draft and edit in passes, integrate code samples and visuals, publish, gather
feedback, measure quality, organize for findability, and maintain over time. Done
well, documentation shortens the path from "what is this?" to "I shipped with it,"
and it surfaces design problems early - a procedure that is painful to document is
often a service that is painful to use.

## Core concepts

**Audience first.** All documentation decisions follow from who the reader is and
what they are trying to do. Research the audience (interviews, support tickets,
existing analytics) and distill the findings into reusable artifacts:

- **User personas** - semi-fictional profiles (an amalgam of real users) capturing
  a representative reader's goals, background, and constraints. Pick the personas
  you are writing for.
- **User journey maps** - the path a user takes toward a goal, showing where
  documentation must intervene.
- **Friction log** - a record kept while you (or a user) walk through a real task,
  noting every point of confusion or delay; the single most direct way to find
  documentation (and product) gaps.

**Content types.** Different reader goals call for structurally different
documents. Docs for Developers organizes content into four types, the same
quadrants popularized by the Divio / Diataxis framework:

- **Tutorials (learning-oriented procedural)** - a guided lesson that takes a
  beginner end-to-end through a meaningful first success; optimize for learning and
  confidence, not coverage.
- **How-to guides (task-oriented procedural)** - steps that solve a specific real
  problem with the service; action over teaching. The true differentiator: one
  document that gets a user to their goal.
- **Reference (information-oriented)** - the exhaustive, precise source of truth:
  API references, configuration, error codes. Best generated from annotated source
  to stay in sync. Concise, minimalistic, complete.
- **Conceptual (understanding-oriented)** - explains the ideas and "how it works"
  behind the service; opinionated allowed, implementation detail excluded. Limit to
  one core concept per document.

(Diataxis names the same four as tutorials, how-to guides, reference, and
explanation, arranged on the axes learning-vs-using and practical-vs-theoretical.)

**Functional vs structural quality.** A document is *good* when it fulfills its
purpose. Two dimensions:

- **Functional quality** - does the doc do its job (accessible, purposeful,
  findable, accurate, complete)?
- **Structural quality** - is it well written (clear, concise, consistent,
  well-organized)?
Functional quality is necessary; structural quality makes it usable.

**Docs-as-code.** Treat documentation like source code: write in plain-text markup
(Markdown), keep it in version control next to the code, review it via pull
requests, and publish through an automated pipeline. This brings the engineering
toolchain - diffs, branches, reviews, CI - to writing, keeps docs near the code so
they are updated together, and lowers the barrier for engineers to contribute.

## Frameworks & models

### The documentation lifecycle (Docs for Developers)
1. **Understand your audience** - research; build personas, journey maps, a
   friction log; test assumptions.
2. **Plan** - decide which content types you need and outline them; define the
   purpose of each document up front so you can later measure success.
3. **Draft** - write to the plan; get words down before polishing; lead with the
   reader's goal.
4. **Edit** - in separate passes (technical accuracy, completeness, structure,
   clarity, brevity, consistency); editing is a distinct skill from drafting.
5. **Integrate code samples** - working, copyable, minimal, with comments that
   show what to replace.
6. **Add visual content** - diagrams, screenshots, and tables where they carry the
   load better than prose; maintain them like text.
7. **Publish** - decide delivery (site, in-product, README) and ship through a
   repeatable process.
8. **Gather and integrate feedback** - provide a feedback channel (e.g. a
   "what's wrong/missing?" template) and act on it.
9. **Measure quality** - set goals, choose metrics, baseline, interpret in
   context.
10. **Organize (information architecture)** - structure for search and navigation.
11. **Maintain and deprecate** - plan for staleness; remove or archive
    out-of-date docs.

### Document templates
- **How-to guide:** intro paragraph (core concepts/overview) -> prerequisites
  (with an "escape hatch" so a misrouted reader can leave) -> numbered steps (one
  action each) -> next steps (links at the bottom, not mid-text). Keep it on a
  single page; minimize in-line links that lead readers away from the task.
- **Conceptual guide:** intro paragraph -> overview of how the concept works ->
  related concepts in sub-sections -> additional resources. One concept per doc.
- **API reference:** introduce standards and response format (e.g. REST/JSON) ->
  authentication -> every resource/endpoint with methods, parameters, an example
  request and response -> status codes and error messages with how to resolve
  them. Generate from annotated code where possible.
- **README (entry point):** project purpose -> installation -> examples/usage ->
  troubleshooting -> changelog -> additional resources -> license. The README is
  the front door; keep it close to the code.

### Information architecture for docs
Information architecture is how content is organized so users can find and navigate
it. Symptoms of poor IA: users cannot find docs that exist ("I know it's buried
deep in the site"). Approach: audit existing content, define categories from user
tasks (not internal team structure), outline a new structure, migrate, and
maintain it as content grows. Good IA, combined with search, lets engines and
navigation route users to the right page rather than a wrong one.

### Measuring documentation quality
- **Tie metrics to goals** at three levels: organizational goals, user goals, and
  documentation goals. Ask why you are measuring and what you will do with the
  result before collecting.
- **Establish a baseline** before you change anything, so you can attribute impact.
- **Interpret in context** - the same metric means different things for different
  docs (rising views on "getting started" is good; rising views on the error-codes
  page may signal product or doc problems).
- **Use clusters of metrics, not a single number** - correlate related signals to
  avoid wrong conclusions (e.g. rising support tickets could mean failing docs *or*
  a surge of new users).
- **Mix qualitative and quantitative** - page metrics and search analytics are
  easy to collect, but user studies, support issues, and direct feedback explain
  *why*.

### Markdown as the documentation substrate (Wieteska)
Markdown is the lightweight plain-text format underpinning docs-as-code: headers
(`#`), emphasis, ordered/unordered lists, links and images, inline and fenced code
blocks, blockquotes, tables, task lists, footnotes, and embedded HTML or Mermaid
diagrams where supported. Conventions: consistent header hierarchy, documentation
kept next to code (README.md in the project root), and writing for clarity over
verbosity.

## Patterns

- **Audience persona** - Intent: distill research into a representative reader so
  every writing decision can be checked against a real goal. When to use: at the
  start of any documentation effort. Trade-offs: requires real research to avoid
  fiction; prevents writing for an imagined reader. *(technical-documentation.md)*
- **Friction log** - Intent: walk a real task and record every point of
  confusion to expose doc and product gaps. When to use: before and after writing,
  and on every new feature. Trade-offs: time to perform honestly; yields the most
  actionable gap list available. *(technical-documentation.md)*
- **Four content types (Divio/Diataxis)** - Intent: match document structure to
  reader intent - learn, do, look up, understand. When to use: planning a docs set;
  diagnosing a doc that tries to be everything at once. Trade-offs: requires
  splitting mixed docs; produces focused, maintainable content. *(technical-documentation.md)*
- **Escape hatch** - Intent: signal to a misrouted reader that they are in the
  wrong place and point them somewhere better. When to use: in prerequisites and
  intros of procedural docs. Trade-offs: minimal; reduces frustration and wasted
  time. *(technical-documentation.md)*
- **Generated reference from annotated source** - Intent: keep API/config
  reference in sync with code by generating it from in-code annotations. When to
  use: any API surface that changes with the code. Trade-offs: tooling setup and
  annotation discipline; eliminates manual drift. *(technical-documentation.md)*
- **Docs-as-code** - Intent: author docs in Markdown, version them with the code,
  review via pull requests, and publish via a pipeline. When to use: any
  engineering org that already uses Git and CI. Trade-offs: writers must learn the
  toolchain; docs stay current and contributable. *(technical-documentation.md)*
- **Metric cluster** - Intent: judge doc quality from a correlated set of signals
  rather than one number. When to use: measuring documentation effectiveness.
  Trade-offs: more to collect and reason about; avoids misleading single-metric
  conclusions. *(technical-documentation.md)*

## Principles & best practices
- **Define the purpose of each document** before writing, so success can be
  measured later.
- **One job per document** - one concept per conceptual doc, one problem per
  how-to, one task per tutorial.
- **One action per step** in procedures; keep procedures short and avoid lengthy
  in-step explanations (move them to a concept doc).
- **Keep the reader on the page** - put links at the bottom; too many in-line
  links pull users away from their task.
- **Edit in passes**, treating editing as separate from drafting.
- **Working, minimal, copyable code samples**, with comments marking what to
  replace.
- **Keep docs near the code** and update them in the same change that changes the
  behavior.
- **Plan for maintainability and deprecation** - assign ownership, review on a
  cadence, and remove docs that have gone stale rather than letting them mislead.
- **Organize around user tasks**, not the org chart.
- **A hard-to-document procedure is a product smell** - feed it back to the
  development team.

## Anti-patterns & pitfalls
- **Writing for yourself** - documenting from the author's mental model instead of
  the reader's goals.
- **The everything document** - one page that mixes tutorial, reference, and
  concept, serving no reader well.
- **Link maze** - so many in-line links that users drown in open tabs and drift
  from their task.
- **Manual API reference** - hand-maintained reference that silently drifts out of
  sync with the code.
- **Buried content** - docs that exist but cannot be found because the information
  architecture or search is poor.
- **Vanity metrics** - chasing page views in isolation without tying them to user
  or business goals or interpreting them in context.
- **Stale docs** - unmaintained content that actively misleads; deprecate or fix.
- **Skipping the edit pass** - shipping a first draft as if drafting and editing
  were the same activity.

## Decision guidance
- **Which content type to write:** ask what the reader is trying to do. Learning a
  new system -> tutorial. Solving a concrete problem -> how-to. Looking up exact
  behavior -> reference. Understanding why/how -> conceptual. If a draft serves two
  intents, split it.
- **Where docs live:** prefer docs-as-code in the same repository as the code when
  the audience is developers and the org uses Git/CI; use a hosted docs site for
  larger, navigable content; keep a README as the entry point regardless.
- **Build vs generate reference:** generate from annotated source whenever the API
  changes with the code; hand-write only stable conceptual framing around it.
- **What to measure:** start from organizational and user goals, baseline,
  then track a small cluster of metrics plus qualitative feedback; never a lone
  number.
- **How much to write:** match depth to audience and adoption risk; favor a few
  high-quality, well-placed docs over exhaustive coverage that cannot be
  maintained.
- **When to deprecate:** if a doc is no longer accurate and not worth updating,
  remove or archive it - stale docs cost more than missing ones.

## Source attribution
- Audience research, personas, journey maps, friction logs, the four content
  types, document templates (how-to, conceptual, API reference, README), the
  draft/edit lifecycle, code samples and visuals guidance, publishing, feedback,
  functional vs structural quality, metric clusters and measurement guidance, and
  information architecture all come from *Docs for Developers* (Corleissen et al.,
  2021), Chapters 1-11.
- Markdown syntax and conventions, the docs-as-code pairing of Markdown with Git,
  README structure, and the "documentation near code / clarity over verbosity"
  best practices come from *Code, Document, Control* (Wieteska, 2025), Chapters 3
  and 5.
- The Divio/Diataxis naming is widely-used industry context noted alongside the
  Docs for Developers content types.

## Recent developments (2024-2026)
The craft fundamentals (audience-first, content types, drafting in passes, friction logs) and the Diataxis quadrants are stable and, if anything, more central. The big shifts are AI as both author and reader of documentation.

- **Diataxis is reinforced, not replaced, by AI.** The four-type split (tutorials, how-tos, reference, explanation) is now encoded as AI agent "skills" that teach assistants (Claude Code, GitHub Copilot, Cursor) to keep documentation types separate when generating or editing docs, grounding AI output in established structure. (idratherbewriting.com, "What is Diataxis," 2025; romainlespinasse.dev, "Diataxis Meets AI," 2025; explainx.ai, "writing-documentation-with-diataxis" skill, 2025.)
- **AI-assisted authoring moves into the toolchain.** Docs platforms add agents that generate structured first drafts from natural-language prompts or from analyzing a commit's code changes, then route them to a human for review, shortening the path from change to published doc. (ekline.io, "Diataxis framework guide," 2025; documentation.ai, 2025.)
- **llms.txt emerges as a convention for AI-consumable docs.** A proposed standard placing a curated, Markdown-linked llms.txt file at a site root so LLMs and agents can find and load the most relevant documentation efficiently rather than parsing rendered HTML. Adoption is broad and growing (BuiltWith tracked roughly 844,000 sites by late October 2025, including Anthropic, Cloudflare, and Stripe), though major AI platforms have not officially confirmed they read it. (getpublii.com, "Complete Guide to llms.txt," 2025; yotpo.com, "What Is LLMs.txt," 2025; developer.mastercard.com, "Working with llms.txt," 2025.)
- **Documentation is now written for two audiences: humans and agents.** Teams increasingly treat AI assistants as a class of reader that values semantic clarity, explicit structure, and machine-readable formats, an extension of "audience first" to non-human consumers. (medium.com / Google Cloud Community, "Give Your AI Agents Deep Understanding With llms.txt," 2025.)
- **Wieteska's "Code, Document, Control" (2025) keeps docs-as-code current.** Treating docs as version-controlled, reviewed plain-text artifacts in the same pipeline as code remains the recommended practice and is what makes both AI drafting and llms.txt generation tractable.
