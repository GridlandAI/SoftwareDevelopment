# UX Design Process and Principles

**Domain:** 14 - Information Architecture and UX
**Primary sources:** Information Architecture and UX Design: The Integration of Information Spaces (Ding, Lin & Zarro, 3rd ed., 2025); Practical UX: A Hands-on Guide to Getting Started (Thomas, 2025)

## Overview

User experience (UX) design is the discipline of shaping how people experience a product so that they are informed, make good decisions, enjoy their interactions, and make progress toward their goals. Information architecture (IA) is the structural backbone of that experience: the organization, labeling, navigation, and search systems that make information findable and understandable. The two are inseparable. IA designs the structure; UX designs the experience of moving through it. This file covers the integrated process (research, personas, journeys, information organization, interaction and visual design, prototyping, and evaluation), the usability heuristics and accessibility principles that govern quality, and how IA and UX combine in practice.

The two sources are complementary. Ding, Lin and Zarro provide the academic, integrated treatment grounded in human-centered design (HCD), ISO 9241, information science, and a research-to-design framework, with strong coverage of organization systems and design patterns. Thomas provides the hands-on consulting practitioner's view: a repeatable, accessibility-first website evaluation built around POUR principles and a ten-tab heuristic checklist, plus how to synthesize, prioritize, and present findings to stakeholders. Because both books are 2025, their content (including AI sections) reflects current practice.

## Core concepts

**IA and UX defined.** IA is the structural design of shared information environments (organization, labeling, navigation, search). UX is the totality of a person's perceptions and responses from using a system. Ding, Lin and Zarro frame the modern object of design as *information spaces* that span web, mobile, wearables, sensors, IoT, and now generative AI, while insisting that the underlying human-centered principles are *timeless* because technology evolves fast but humans evolve slowly.

**Human-centered design (HCD).** Per ISO 9241-210, HCD is an iterative process that combines user needs and business goals, includes users throughout research and design, guides design ideas, and aids new-technology adoption. It runs as a loop: understand context of use, specify requirements, produce design solutions, and evaluate against requirements, repeating until the design is good enough.

**Qualitative vs. quantitative data.** Quantitative data is numbers ("what happened": Participant B took 90s vs. a 60s target). Qualitative data is words ("why it happened": "the navigation is cluttered and confusing"). The two are most powerful together: numbers find the problem, words explain it. Qualitative usability studies need only a handful of participants ("if one person falls in a pothole, you know to fix it"); quantitative studies (A/B, surveys) need statistically significant sample sizes.

**Personas.** Research-grounded archetypes of users that align the team on who is being designed for, build empathy, and support decisions and cognitive walkthroughs. Constructed from research data, not invented.

**User journeys.** Maps of a persona's path through a task or experience over time, exposing pain points, emotions, and opportunities. Built from research to reveal where the experience breaks down.

**Information organization.** The structures that make content findable: logical organization, semantic organization via metadata (standardization, indexing quality), controlled vocabularies, taxonomies, faceted classification, and tagging. Controlled vocabularies trade flexibility for consistency and findability.

**Navigation and search systems.** Navigation types (global, local, contextual, supplemental, breadcrumb) plus search systems that satisfy needs when browsing fails. AI agents increasingly mediate both.

**Wireframing and prototyping.** Low-fidelity wireframes communicate structure and layout cheaply; higher-fidelity prototypes test interaction and flow. Fidelity should match the question being asked.

**Interaction design (IxD) and its five dimensions.** Crampton-Smith's four dimensions plus Silver's fifth: *words* (clear, consistent labels with the right tone, often IA's work), *visual representations* (graphics, icons, type), *physical objects/space* (the device and where it is used), *time* (motion, sound, duration), and *behavior* (how users act and how the system responds).

**Views, forms, and workflows.** Views display and navigate; forms create and edit data; combined they form workflows. Workflow structures: *hubs* (a primary view with independent one-page sub-forms, e.g. email, calendars), *wizards/serial processes* (a fixed sequence with Back/Next, e.g. checkout, installers), and *guides* (hubs plus wizards).

**Accessibility and POUR.** WCAG organizes accessibility under four principles: content must be **P**erceivable, **O**perable, **U**nderstandable, and **R**obust. These apply to any digital product regardless of technology and benefit everyone, not only users with disabilities.

## Frameworks & models

**HCD design process (ISO 9241-210).** Iterative loop of context, requirements, design, and evaluation, with users involved throughout and deliverables (personas, journeys, IA, wireframes, prototypes) at each stage. Distinguishes front-end UI design from behind-the-scenes work (metadata, controlled vocabulary) and design deliverables.

**Research-to-design framework (Ding, Lin & Zarro).** A structured way to move from understanding to solution using five inputs: *thinking and experience* (designer judgment), *theory* (human information behavior), *patterns and principles*, *secondary research* (existing literature/data), and *primary research* (new studies with users). The framework guides which inputs to draw on for a given problem.

**Design thinking to systems thinking.** Design thinking is a human-centered process for generating new solutions (commonly empathize, define, ideate, prototype, test), using a "zoom in" mindset on the user. Systems thinking complements it by "zooming out" to map the whole system of actors, flows, and feedback loops; system mapping reveals leverage points and unintended consequences that user-level design misses.

**Nielsen-style usability heuristics (operationalized by Thomas).** Thomas turns the classic heuristics into a ten-tab scored evaluation checklist:
1. **Wayfinding and organization** - users know where they are and what to do next (active-page indicators, breadcrumbs, keyword-first labels, progress in multi-page flows).
2. **Visibility of system status** - clear feedback within reasonable time (enabled vs. disabled fields, obvious actionable content, selected-state highlights, sort/filter indicators, feedback for meaningful actions, sub-second widget response, progress for processes over two seconds).
3. **Content clarity and readability** - right medium, right reading level (vertical readable labels, choose-one vs. choose-many distinctions, body text at least 16px, larger fonts for metrics, unambiguous internationalized dates, clearly labeled data).
4. **User control and freedom** - informed choices, ability to go back in multi-step flows, cancel in progress, confirm destructive actions, easy undo/redo, edit/delete of user data.
5. **Consistency and standards** - important information first, consistent icon scheme, consistent label placement, conventional color meaning, semantic and grammatical consistency, consistent number/currency/symbol formats.
6. **Recognition rather than recall** - consistent placement and appearance of visual/text prompts so users recognize rather than remember.
7. **Flexibility and efficiency of use** - accelerators and adaptable paths for novice and expert.
8. **Aesthetic and minimalist design** - no irrelevant or rarely needed information competing for attention.
9. **Error prevention and recovery** - prevent errors first, then help users detect and recover with clear, specific messages.
10. **Help and documentation** - help available, searchable, task-focused, and ideally unnecessary because the design is intuitive.

**Interaction design principles (Ding, Lin & Zarro).** Design for Fitts's Law (bigger and closer targets), affordance (perceived affordance via shape, color, font, location, grouping, metaphor), efficiency (reduce data entry and decisions, auto-fill, constrain choices), forgiveness (allow imperfection), easy reversal of actions (undo, confirm before catastrophe), error prevention (constrain input, instruct upfront, isolate errors with wizards), error handling (constructive, specific recovery messages), user perceptions (manage perceived time, e.g. loading indicators, mirrors-in-elevator-lobby effect), help (make it unnecessary, then contextual), inclusion (support the full range of human diversity), and personalization (system-generated views) vs. customization (user-controlled changes).

**Scoring and prioritization (Thomas).** Each checklist item is scored Always / Usually / Sometimes / Rarely / Never / N/A across pages, with a Priority flag for obvious, egregious barriers. Synthesis identifies must-fix issues, prioritizes them, and turns them into ranked recommendations with visuals, called out for legal and financial implications, then presented to stakeholders in a slide deck.

## Patterns

- **Hub workflow** - Intent: present a primary view with independent, editable sub-elements. When to use: dashboards, email, calendars, profile editing where sub-tasks have no dependency. Trade-offs: weak guidance for ordered processes; great for non-linear editing. *(Ding, Lin & Zarro)*
- **Wizard / serial process** - Intent: break a complex task into a fixed sequence of simple steps with Back/Next and a final summary. When to use: checkout, account setup, installers. Trade-offs: rigid and slower for experts; isolates errors and reduces cognitive load for novices. *(Ding, Lin & Zarro)*
- **Faceted classification / filters** - Intent: let users narrow large result sets along multiple independent dimensions. When to use: e-commerce, search, large catalogs. Trade-offs: needs clean metadata; too many facets overwhelm. *(Ding, Lin & Zarro)*
- **Typeahead / auto-complete** - Intent: reduce data entry and misspellings by suggesting valid input as the user types. When to use: search, address and product fields. Trade-offs: implementation cost; can mislead if suggestions are poor. *(Ding, Lin & Zarro)*
- **Confirm-before-catastrophe** - Intent: insert deliberate friction (an "are you sure?" dialog or typed confirmation) before irreversible, high-cost actions. When to use: delete, overwrite, send, pay. Trade-offs: friction on every use; calibrate to the damage if the action is not reversible. *(Ding, Lin & Zarro; Thomas)*
- **Progress indicator** - Intent: show step position and percent complete to reduce anxiety and sustain motivation. When to use: any multi-step flow or process over ~2 seconds. Trade-offs: adds UI; required for processes that take perceptible time. *(Thomas; Ding, Lin & Zarro)*
- **Keyword-first labels** - Intent: lead menu and button labels with the meaningful keyword ("Add to Cart" not "Click here to add") so they are scannable and screen-reader friendly. When to use: all navigation and action labels. Trade-offs: none significant; improves findability and accessibility. *(Thomas)*
- **Cope-with-choice toolkit** - Intent: reduce decision paralysis by setting good defaults, using visible and invisible filters, and organizing choices hierarchically. When to use: any interface with many options. Trade-offs: defaults must serve the user's interest; over-filtering hides valid options. *(Ding, Lin & Zarro)*

## Principles & best practices

- Treat IA and UX as one design act: structure and experience are two sides of the same coin.
- Run HCD as an iterative loop, involving real users throughout, not as a one-time gate.
- Pair quantitative and qualitative research: numbers locate problems, words explain them; five users suffice for qualitative usability discovery.
- Match prototype fidelity to the question; do not polish before you have validated structure.
- Design accessibility in from the start (POUR), benefiting all users; never rely on color alone, ensure keyboard operability, 44x44px minimum targets, visible focus indicators, meaningful titles and headings, and discernible link text.
- Apply Fitts's and Hick's laws: make important targets big and close, and limit choices in simple time-sensitive decisions.
- Make affordances perceivable; non-actionable items must not look actionable.
- Prevent errors before handling them; when they occur, give specific, constructive recovery guidance.
- Manage perceived time, not just real time (loading indicators, engagement during waits).
- Default to good defaults; most users never customize.
- Use response-time guidelines as concrete engineering requirements (sub-0.1s for direct-manipulation feel, ~1s to keep control, ~10s as the attention ceiling; Doherty threshold of 400ms for productivity).
- Synthesize and prioritize findings into ranked, business-aware recommendations; present them clearly to stakeholders.

## Anti-patterns & pitfalls

- **Polishing visuals before validating structure**, mistaking surface for usability.
- **Skipping research** and designing from assumptions or a single subject-matter expert's opinion.
- **Color-only status**, vague link text ("click here"), and tiny touch targets, all accessibility failures.
- **Over-customization**, offloading organization onto users who will not do it (paradox of choice, low customization rates).
- **Wizards for non-linear tasks** (or hubs for strictly ordered ones), mismatching workflow structure to task dependency.
- **Unhelpful error messages** that state that something failed without telling the user how to recover.
- **Treating accessibility as a late compliance checkbox** rather than a design input.
- **Presenting raw findings without prioritization**, leaving stakeholders unable to act.

## Decision guidance

- Choose a research method by starting from the research question, then constraints, then participants and presentation. Use usability testing plus interviews for "why" problems; card sorting and tree testing for IA structure and mental models; A/B testing and surveys for quantitative validation at scale; contextual inquiry and diary studies for real-world behavior over time; heuristic and expert evaluation when fast, low-cost inspection is acceptable (no real users).
- Choose a workflow structure by task dependency: independent sub-tasks favor a hub; strictly ordered, complex tasks favor a wizard; mixed favors a guide.
- Calibrate confirmation friction to the cost of the error: irreversible, high-damage actions justify confirmation or typed acknowledgment; trivial, reversible actions should stay frictionless with undo.
- Use Hick's Law for simple time-sensitive choices (limit options) but not blindly for recognition-based menus, where mega-menus exceeding "seven items" work because users recognize rather than recall.
- Prioritize accessibility fixes by impact: obvious, egregious barriers that block task completion get top priority; use the scored checklist to surface them and decide whether in-house fixes suffice or an accessibility specialist is needed.
- Zoom in with design thinking to solve the user's immediate problem; zoom out with systems thinking when the problem has many stakeholders, channels, or feedback loops.

## Source attribution

- *IA/UX definitions, information spaces, HCD and ISO 9241, qualitative vs. quantitative data, research and evaluation methods table, personas and journeys, information organization (metadata, controlled vocabularies, taxonomies, facets, tagging), navigation and search, interaction design principles and the five dimensions, views/forms/workflows (hubs, wizards, guides), filters and controls, research-to-design framework, design thinking to systems thinking, response-time guidelines (powers of 10, Doherty threshold)* - Ding, Lin & Zarro, *Information Architecture and UX Design* (3rd ed., 2025), Chapters 1-9.
- *Accessibility-first website evaluation, POUR principles, the ten-tab heuristic checklist, scoring and prioritization, synthesis and recommendations, stakeholder reporting, keyword-first labels, target sizes, focus indicators, meaningful titles/headings/link text* - Thomas, *Practical UX* (2025), Chapters 4-6.

## Recent developments (2024-2026)
Both primary sources are 2025 and current. The HCD loop, heuristics, and POUR accessibility principles are stable. The active changes are AI in the process and accessibility becoming law.

- **AI joins every stage of the HCD loop.** Practitioners use generative AI to draft personas and segments from behavioral data, generate wireframe/structure variations from text or sketches, and accelerate synthesis, while keeping the designer on higher-level problem framing and validation. The discipline's stance holds: AI generates options; humans define needs and verify. (cqlsys.com, "How Generative AI is Transforming UI/UX Design in 2025"; Fuselab Creative, "AI-Generated UI Design," 2025.)
- **Adaptive and conversational interfaces become mainstream UX patterns.** Five named AI UX patterns (predictive design, generative assistance, adaptive personalization, background automation, conversational interfaces) now sit alongside the classic heuristics, aiming to cut the number of interactions. (LogRocket Blog, "AI-driven UX design patterns," 2025; shapeof.ai, 2025.)
- **Accessibility shifted from best practice to legal mandate in the EU.** The European Accessibility Act took effect June 28, 2025 across all 27 member states, adopting EN 301 549 (WCAG 2.1 AA, with WCAG 2.2 AA expected as the standard updates by end of 2025). Fines reach into six figures (for example up to EUR 100,000 per violation in Germany), and surveillance began in late 2025. This makes the POUR-based, accessibility-first evaluation in this file a compliance requirement, not just a quality goal. (levelaccess.com, "EAA Compliance Guide," 2025; en.wikipedia.org, "European Accessibility Act"; metadrop.net, 2025.)
- **Ethical guardrails against AI-driven dark patterns gain prominence.** A notable share of designers worry that adaptive UI optimized for conversion will scale dark patterns, sharpening the field's emphasis on informed choice, user control, and honest defaults. (cqlsys.com, 2025; LogRocket, 2025.)
