# Information Architecture Practice (Process and Deliverables)

**Domain:** 14 - Information Architecture and UX
**Primary sources:** *Information Architecture: Blueprints for the Web*, 2nd ed. (Wodtke & Govella, 2009); *A Practical Guide to Information Architecture* (Spencer, 2010); *Everyday Information Architecture* (Martin, 2019); *How to Make Sense of Any Mess* (Covert, 2014); *Information Architecture for the World Wide Web*, 2nd ed. (Rosenfeld & Morville, 2002)

## Overview

This file covers *how IA work actually gets done*: the process from research to launch, and the concrete deliverables that carry decisions through a project. Where the companion file `information-architecture.md` covers the systems and theory, this one covers the craft - audits, card sorts, tree tests, sitemaps, wireframes, blueprints, and the sensemaking process for messes.

The practice books converge on a non-linear but recognizable flow: understand context, people, and content; synthesize a draft structure; test it; document it; and prepare to adjust. The unifying message is that IA is "closer to art than science" (Martin) - you blend instinct, nerves, and as much data as you can gather - but it is *disciplined* art, grounded in research and made tangible through artifacts that get a team to a shared vision. "A blueprint is good thinking written down" (Wodtke & Govella).

## Core concepts

**IA process overview (Rosenfeld & Morville).** A research-driven cycle: **Research** (context, content, users) -> **Strategy** (a defensible direction and work products) -> **Design and documentation** (blueprints, wireframes, content models, vocabularies) -> **Administration** (ongoing maintenance). Research frames everything; strategy turns research into direction; documentation makes it buildable.

**The non-linear reality (Spencer; Covert).** IA is not a waterfall. You loop between understanding and proposing. Spencer's blunt advice for creating an IA: do the preparation (research + content), then "just make it up" based on what you know, then *check it* - because no first draft works perfectly. Covert frames the whole journey as iterative sensemaking with an explicit "Prepare to Adjust" step.

**Findability and the user's information needs (Rosenfeld & Morville; Spencer).** Design for how people actually look for information. Spencer's information behaviors:
- **Known-item finding** - user knows what they want, has words for it, knows the answer when they see it (most common; "jump in, get answer, leave").
- **Exploratory** - user has a fuzzy need that evolves as they look.
- **Don't-know-what-you-need-to-know** - user cannot articulate the need yet.
- **Re-finding** - returning to something found before.

A single IA must serve all of these, which is why search and browse are complementary.

## Frameworks & models

### Research: context, content, users (Rosenfeld & Morville; Spencer)

- **Context research** - business/organizational goals, stakeholders, culture, politics, technology, resources. Spencer's "before you start": define goals, audiences, constraints.
- **Content research** - learn what content exists and what is needed (see content inventory/audit below).
- **User research** - card sorts, think-aloud usability sessions, interviews, contextual inquiry, surveys, search-log and analytics analysis. Spencer warns surveys lack richness (no follow-up probing) and should support, not replace, other methods. Recruit real users or proxy advocates (librarians, SMEs, switchboard operators) when users are hard to reach.

### Covert's seven-step sensemaking process (How to Make Sense of Any Mess)

A domain-independent process for taming any "mess" (a situation where interactions between people and information are confusing):

1. **Identify the Mess** - outline its edges and depths; name users, stakeholders, and the information being interpreted. Diagnose whether you have too much, too little, or the wrong information.
2. **State Your Intent** - intent is language. Choose adjectives you *want* users to use and adjectives you are okay *not* having. Define what "good" means for this context (good is subjective). Start with *why*, then *what*, then *how*.
3. **Face Reality** - account for players (current/potential users, stakeholders, competitors, distractors) and factors (time, resources, skillset, environment, politics, ethics). Map across channels and contexts. Make objects (diagrams, maps) to externalize mental models. Covert's toolbox of ten: block, flow, Gantt, quadrant, Venn, swim-lane, hierarchy, mind map, schematic, journey map - plus the matrix diagram.
4. **Choose a Direction** - work at the right level (object -> interface -> location -> journey -> structure -> system -> ecosystem). Practice *placemaking*. Reduce *linguistic insecurity* with a controlled vocabulary; understand *ontology* (deciding what words mean in your context) vs. lexicography. Build a list of words you *say* and, powerfully, words you *don't say*. Combine nouns + verbs into requirements.
5. **Measure the Distance** - turn intent into goals (intent + baseline + progress). Define indicators, baselines, and flags. Measure progress, not just success.
6. **Play with Structure** - choose a structure that makes sense to users, reflects intent, and reaches goals. Taxonomy (classification + sorting); classification is the hard part, sorting is easy once rules exist. Exact vs. ambiguous classification (ambiguity costs clarity; exactitude costs flexibility). Patterns: hierarchy, heterarchy, sequence, hypertext - usually combined. Think with boxes and arrows before committing.
7. **Prepare to Adjust** - don't seek finalization; don't procrastinate. Get agreement up front; "design with, not for"; be the *filter, not the grounds*.

### Personas, scenarios, task analysis (Wodtke & Govella)

- **Personas** - archetypal users built from research ("playing Barbies for designers"). Process: gather data; brainstorm; prioritize/cull into primary and supporting personas; make them real people; apply them to design decisions.
- **Scenarios** - narratives of a persona accomplishing a goal ("the Joseph Campbell method"); used to walk a feature through a real journey (e.g., the Festival Planner).
- **Task analysis** - diagramming all the steps a user takes to complete a task, which then drives page design (match discrete tasks to discrete pages; group like tasks).

## Patterns (methods and deliverables)

### Research and analysis methods

- **Content inventory** - Intent: list everything on the site (pages, downloads, embedded media, comments) so you fully understand content before designing IA. When to use: any content-heavy redesign or migration. Trade-offs: a *full* inventory is hugely valuable but expensive (Spencer: 30,000-page site took two people a month); use a *partial* inventory (top N levels + spot-deep) for very large sites, or a *content audit* (qualitative assessment) when you need judgment over completeness. (Spencer; Martin)
- **Content audit (qualitative)** - Intent: assess content for purpose, quality, accuracy, ownership, and fate (keep/revise/delete/migrate). When to use: alongside or instead of a raw inventory, especially to inform strategy. Trade-offs: subjective; pair with quantitative inventory data. Martin's audits also capture purpose, user need, business goal, current vs. future state. (Martin; Spencer)
- **Card sorting** - Intent: discover how users group and name content, revealing their mental models and category labels. When to use: building or validating an organization scheme. **Open** card sort = users create and name their own groups (generative); **closed** card sort = users sort into predefined groups (evaluative). Process (Wodtke & Govella's recipe-file exercise): dump items, mix, group by affinity, name the piles; run with several target users; look for the dominant scheme, fold in oddities, set aside one-offs, resolve into a taxonomy. Trade-offs: time-consuming; results need synthesis; only as good as your participant selection. (Wodtke & Govella; Spencer)
- **Tree testing / task-based IA assessment** - Intent: validate a *draft hierarchy* (groups + labels) before building navigation or pages, by asking users where they would look for given tasks. When to use: as soon as you have a draft IA you are happy with; fast (set up in a morning, results by afternoon). Trade-offs: tests structure/labels in isolation (no visual design), which is also its strength - it isolates whether labels and groupings work. Best for hierarchy, simple database, and focused-entry-point structures. (Spencer's "testing IA"; Martin references it under Resources)
- **Search-log and analytics analysis** - Intent: learn the real words users type and where they drop off. When to use: any existing site. Trade-offs: tells you *what* but not always *why*. (Rosenfeld & Morville; Spencer; Martin "working with automated data")
- **Surveys** - Intent: gather many responses cheaply; compare before/after. When to use: supplementary research. Trade-offs: shallow data, no probing. (Spencer)

### Synthesis and structuring methods

- **"Just make it up, then check it"** - Intent: overcome blank-page paralysis by drafting an IA from everything you have learned, then validating it. When to use: after sufficient research, at the synthesis step. Trade-offs: only works if grounded in research; the *check* step is the important one. (Spencer)
- **Criteria matching** - Intent: define explicit criteria for what belongs in each category, then test labels and categories together against user needs, business goals, current state, and future state. When to use: turning content into categories. Trade-offs: iterative; labels and categories co-evolve (Martin's Pinterest "Cookies & Bars" example - sort until a recipe defies the criteria, then adjust). (Martin)
- **Focused entry points** - Intent: keep one primary structure (usually a hierarchy) but add task- or audience-based entry points for users who can't find things via the main scheme. When to use: large sites serving diverse needs where one organization can't suit everyone. Trade-offs: entry points need not cover all content; better than forcing task/audience as the whole structure. (Spencer)

### Deliverables / documentation

- **Sitemap (a.k.a. blueprint, structural map)** - Intent: document the pages of a site and their hierarchical/navigational relationships. When to use: communicating structure, getting stakeholder approval, planning migration/wireframing. Forms: **box-and-arrow diagram** (high-level, best for stakeholders and small sites), **outline** (text, completionist, every page at every level), **spreadsheet** (rich metadata - source URLs, new URLs, owner, template, migration status). Use a consistent visual vocabulary - Jesse James Garrett's "Visual Vocabulary" (page, page stack, file, decision diamond, area/conditional area, connectors, continued, e-mail). Include a legend; differentiate single pages from collections; document outliers; aim for editorial accuracy. Trade-offs: a sitemap is just one (limited) artifact; it never launches perfect. (Wodtke & Govella; Martin; Rosenfeld & Morville)
- **Blueprint (Rosenfeld & Morville sense)** - Intent: high-level diagram showing the relationships between pages and components and the navigation routes; "good thinking written down." When to use: conveying the architecture vision. Trade-offs: not the building itself - must be paired with wireframes and content models. (Rosenfeld & Morville; Wodtke & Govella)
- **Wireframe (page schematic)** - Intent: a low-fidelity outline of one page's elements, their relationships, and relative importance - making an abstract page concrete and starting team conversation before expensive visual design. When to use: documenting individual pages after structure is set. Two functions: (1) build shared vision early; (2) serve as downstream documentation for designers, developers, QA. Recipe: start with a box; add global/template elements; add unique elements with order of importance, dynamic content, text/GUI type; then "notate, notate, notate." Trade-offs: easy to over- or under-specify; annotation is essential since rationale is forgotten quickly. (Wodtke & Govella; Rosenfeld & Morville)
- **Zoning and modules** - Intent: divide a page into zones by user need (global nav, content nav, content, metadata/tools), then group tightly related elements into reusable modules. When to use: page-level design. Trade-offs: requires thinking about reuse up front; pays back in consistency and engineering reuse (Wodtke & Govella's Flickr comment/discussion modules). (Wodtke & Govella)
- **Content model / content mapping** - Intent: define content types and their attributes/fields so structure and content fit together and support metadata-driven assembly. When to use: database/faceted structures, CMS-driven sites. Trade-offs: more up-front modeling. (Rosenfeld & Morville)
- **Controlled vocabulary / taxonomy documentation** - Intent: record preferred terms, variants, tags, and the process for managing them over time. When to use: multi-author content, automated content placement, faceted filtering. Trade-offs: needs governance (who adds/retires terms) to avoid folksonomy rot. (Martin; Rosenfeld & Morville)
- **Strategy report and project plan** - Intent: a defensible written strategy (with research findings, recommended direction, work-product list) and a plan to execute it. When to use: securing buy-in and scoping the build. Trade-offs: strategy "under attack" needs evidence; presentations matter. (Rosenfeld & Morville)
- **Architecture style guide** - Intent: rules for maintaining the IA over time (labeling conventions, metadata rules, point-of-production guidance). When to use: handing off to ongoing administration. Trade-offs: requires upkeep. (Rosenfeld & Morville)

## Principles & best practices

- **Understand content before organizing it.** "You can't organize what you don't understand." Be the content advocate; audit early to avoid downstream layout and usability failures (Martin).
- **Do real research; don't design from the org chart or from assumptions.** Use card sorts and behavior data to find user mental models (Spencer; Wodtke & Govella).
- **Test the IA in isolation before building.** Tree testing/task-based assessment finds broken groupings and labels cheaply, before navigation, layout, and content compound the problem (Spencer).
- **Iterate the structure with explicit levers.** When revising, change labels, change categories, change content, or rethink the approach - don't bend content just to fit an ideal sitemap (Martin).
- **Make artifacts to build shared vision, not to show off.** Keep diagrams tidy but pliable; the goal is conversation and feedback, not polish that you become attached to (Covert; Wodtke & Govella).
- **Pick the right documentation fidelity for the audience.** Box-and-arrow for stakeholders, spreadsheets for migration/dev, outlines for completeness (Martin).
- **Govern vocabularies and tags.** Define how terms are added, retired, and enforced; controlled tags beat free-for-all folksonomies (Martin).
- **Get agreement up front and design with users.** Involve stakeholders and users early; "making maps and diagrams alone at your desk is not practicing information architecture" (Covert).
- **Annotate everything.** Documents must live without you looking over the reader's shoulder; obvious decisions become obscure over time (Wodtke & Govella).

## Anti-patterns & pitfalls

- **Skipping the audit because "we're starting fresh."** You still need to understand existing content's scope, patterns, and value, or you repeat old mistakes (Martin).
- **Designing pages before structure.** Jumping to wireframes/visual design before the sitemap and labels are validated leads to rework (Wodtke & Govella; Spencer).
- **Treating the sitemap as final/scientific.** No site launches with a perfect sitemap; expecting an airtight formula wastes time and ignores politics (Martin).
- **Research theater.** Surveys-only research, or card sorts with the wrong participants, yields confident but wrong conclusions (Spencer).
- **Knowing instead of doing.** "Knowing too much can encourage us to procrastinate"; over-analysis lets the mess grow (Covert).
- **Solving the wrong problem.** Jumping into "how" before settling "why" and "what" (Covert).
- **Weak requirements.** "A user can easily publish with one click" smuggles in interaction, interface, and ambiguous quality - lock-in by accident. Strong requirements describe the result, not the how (Covert).
- **Unmanaged folksonomy.** Free author tagging without a controlled list -> bloated, inconsistent, broken filtering (Martin).
- **One-off odd categories in the main scheme.** Surface them as featured items or shortcuts, not as primary categories (Wodtke & Govella).

## Decision guidance

- **Sequence the work.** Research (context/content/users) -> draft structure (synthesize, "just make it up") -> test (card sort to build, tree test to validate) -> document (sitemap + wireframes + vocabulary) -> prepare to adjust. Loop as needed; it is not linear (Spencer; Covert; Rosenfeld & Morville).
- **Choose the inventory depth by site size.** Small site -> full inventory. Huge site -> partial inventory (top levels + targeted deep dives) or qualitative audit. Always inventory before a migration so nothing is lost (Spencer).
- **Choose the research method by question.** "How do users group content?" -> open card sort. "Does my structure work?" -> closed card sort / tree test. "What words do users use / where do they drop off?" -> search logs + analytics. "What do many users think?" -> survey (supplementary). "Why do they do that?" -> think-aloud / interviews (Spencer; Wodtke & Govella).
- **Choose the sitemap format by purpose and audience.** Stakeholder buy-in -> box-and-arrow diagram. Complete page record -> outline. Migration/dev collaboration with metadata -> spreadsheet. You can use more than one (Martin).
- **Decide wireframe fidelity by handoff.** Designer needs rough layout; developers need functionality and error states; QA needs every detail. Notate accordingly (Wodtke & Govella).
- **When stuck, externalize and reframe.** Make a different diagram (Covert's toolbox); arrange the content two or three ways and compare; the act of trying a "wrong" structure reveals the right one (Covert; Wodtke & Govella).
- **Plan for politics.** Revisions come from purity (balance/clarity) and politics (a stakeholder's buried favorite). Both are normal; secure agreement early and bring rationale (reports, annotations) to defend decisions (Martin; Covert).

## Source attribution

- **IA process (research/strategy/design-documentation/administration), information needs, blueprints, wireframes, content mapping/modeling, controlled-vocabulary documentation, strategy report, style guide, defending IA strategy:** *Information Architecture for the World Wide Web*, 2nd ed., Chs. 10-12 (Rosenfeld & Morville, 2002).
- **Personas, scenarios, task analysis, the card-sort exercise, zoning/modules, sitemap with Garrett's Visual Vocabulary, the 30-second wireframe, "good thinking written down":** *Information Architecture: Blueprints for the Web*, 2nd ed. (Wodtke & Govella, 2009).
- **Information behaviors, content inventory vs. partial vs. audit, user-research methods and surveys, card sorting, "just make it up then check it," testing IA / task-based assessment, IA patterns, focused entry points:** *A Practical Guide to Information Architecture* (Spencer, 2010).
- **Audits (purpose/quality/scope), criteria matching, label qualities, sitemap documentation styles (diagram/outline/spreadsheet), making adjustments via four levers, taxonomy/tag governance:** *Everyday Information Architecture* (Martin, 2019).
- **The seven-step sensemaking process, intent/language, ontology and controlled vocabulary (words you say / don't say), requirements from nouns+verbs, diagram toolbox, levels (object->ecosystem), prepare to adjust, "design with not for," "filter not grounds":** *How to Make Sense of Any Mess* (Covert, 2014).

## Recent developments (2024-2026)
The process and deliverables (research, audits, card sorting, tree testing, sitemaps, wireframes, vocabulary) are stable craft. AI changes tooling and adds a new validation target.

- **AI-assisted IA tooling enters the workflow.** Designers increasingly use generative tools to draft sitemaps, label sets, and card-sort analyses from research inputs, then refine and validate them. The discipline's caution stands: AI-generated structure must still be tested with real users (card sort to build, tree test to validate) rather than shipped on trust. (LogRocket Blog, "AI-driven UX design patterns," 2025.)
- **Findability now has a machine audience.** Beyond human tree testing, teams add "AI visibility audits" checking whether structured content and clean metadata let AI search engines retrieve and cite the content (Generative Engine Optimization). This is a new acceptance criterion layered on the existing process, not a replacement for it. (geostar.ai, "GEO guide," 2025; Strapi, 2025.)
- **Controlled vocabularies and structured data gain renewed importance.** The same governed taxonomies and schema that power faceted search and editorial consistency are what make content parseable by RAG-based answer engines, strengthening the case for the vocabulary-governance work this file describes. (arXiv 2509.08919, 2025.)
- **Accessibility moved from best practice to legal requirement in the EU.** The European Accessibility Act took effect June 28, 2025 (EN 301 549 / WCAG 2.1 AA, with WCAG 2.2 AA expected as EN 301 549 updates), raising the stakes on the accessibility-first evaluation that IA/UX deliverables must satisfy. (levelaccess.com, "EAA Compliance Guide," 2025; en.wikipedia.org, "European Accessibility Act.")
