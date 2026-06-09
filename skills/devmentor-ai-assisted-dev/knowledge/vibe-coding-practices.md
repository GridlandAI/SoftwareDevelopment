# Vibe Coding Practices

**Domain:** 11 - AI-Assisted Development
**Primary sources:** *Vibe Coding: Building Production-Grade Software with GenAI* (Kim & Yegge, 2025); *Beyond Vibe Coding: From Coder to AI-Era Developer* (Osmani, 2025); *Vibe Coding: The Future of Programming* (Osmani, 2025); *Vibe Coding: The Coming Code Wave* (Thomsen, 2025); *Professional Software Developers Don't Vibe, They Control* (Huang et al., 2025); *Vibe Coding in Practice* (Waseem et al., 2025)

## Overview

Vibe coding is software development in which a person expresses intent in natural language and an AI model (usually a code-trained LLM) generates, refines, and sometimes runs the code. The term was popularized by Andrej Karpathy in early 2025, originally describing an experience where you "fully give in to the vibes," trust the AI, and stop reading the diffs. In professional practice the term has broadened to mean almost any agentic, conversation-driven coding, and the practitioners in this knowledge base treat it as a spectrum rather than a single behavior. At one end is pure vibing (accept-all, do not read the code) and at the other is disciplined AI-assisted engineering (plan-first, review everything). This file captures what vibe coding is, the workflows that make it productive, the tooling and context strategies it depends on, where it works well, and what the developer experience actually looks like. The engineering rigor that keeps it safe is the subject of the companion file `ai-assisted-engineering-discipline.md`.

Why it matters in the SDLC: vibe coding changes the unit of developer work from typing code to articulating goals, decomposing tasks, and verifying output. It can compress days of work into hours, lower the barrier to who can build software, and shift the human's role from "line cook" to "head chef" orchestrating AI collaborators. The acceleration is real and large, but it is bounded by a well-documented "70% problem" and by the human supervision required to make output production-grade.

## Core concepts

**The AI coding spectrum (Osmani).** Two extremes anchor a continuum:
- *Vibe coding* - prompt-first, exploratory, code-by-conversation. Optimizes for velocity in the short term. Audience: solo builders, prototypers, product-minded non-specialists, and experienced developers brainstorming. You expect surprises.
- *AI-assisted engineering* - plan-first, structured, AI as copilot across the whole SDLC under explicit constraints and acceptance criteria. Optimizes for sustained velocity and reliability. Audience: professional teams with code review, testing, and deployment pipelines they will not abandon.
Real workflows blend both. Experienced developers tend to drift from the vibe end toward the engineering end as they learn where AI shines and stumbles, moving from "prompt artists" to "orchestra conductors." A good AI-assisted engineer uses vibe-style prompting in "microdoses" inside a structured frame (for example, "generate a quick date-format helper") then switches back to engineer mode to integrate and verify.

**Levels of AI coding (Kim & Yegge).** Manual/traditional coding -> code completion (Copilot autocomplete, 2021) -> chat coding (copy/paste from a conversation, 2023-2024) -> agentic coding (AI autonomously reads, modifies, runs, and tests code, early 2025 with Claude Code). Each level removes friction; agentic coding closes the feedback loop because the agent can validate its own work, turning a 50-100% chat speedup into a 5-10x agentic speedup.

**FAAFO (Kim & Yegge).** The benefits framework: Faster, more Ambitious, more Autonomous, more Fun, more Options. Optionality (cheap parallel exploration of alternatives) is presented as the most transformative dimension.

**Programming with intent (Osmani; Thomsen).** The developer's value shifts from translating ideas into explicit machine instructions toward expressing outcomes and letting AI handle translation. "The hottest new programming language is English" (Jensen Huang, quoted by Osmani). Thomsen frames this as the next rung on the decades-long ladder of abstraction (machine code -> assembly -> high-level languages -> frameworks -> vibes), with the same caveat each rung carried: abstraction is good, ignorance is fatal. The laws of computer science (algorithms, complexity, architecture, debugging) still apply underneath.

**The head chef metaphor (Kim & Yegge).** You are the executive chef; AI is your sous chef and brigade of specialist cooks. They have memorized every cookbook, work at lightning speed, never sleep, and occasionally insist on ingredients that do not exist. As head chef you, not the AI, are accountable for every dish that leaves the pass.

## Frameworks & models

**The vibe coding loop (Kim & Yegge).** The day-to-day cycle, structurally similar to the traditional developer loop but where every step becomes critical because the system moves fast:
1. Frame your objective - a clear, concise statement of the outcome and why.
2. Decompose the tasks - break into small, achievable steps; smaller is better for AI success.
3. Start the conversation - ask AI to propose a plan or give it starting instructions.
4. Review with care - the output may look correct, but verify before trusting it.
5. Test and verify - you own the quality whether you or AI wrote it; works best with tests written before generation (TDD).
6. Refine and iterate - continue until the goal is met.
7. Automate your own workflow - remove friction (typing, copy/paste) because it is a tax paid on every loop.

**The three developer loops, each with Prevent / Detect / Correct (Kim & Yegge).** Practices are organized by feedback-loop scope:
- *Inner loop* - the seconds-to-minutes cycle of generating and validating a single change.
- *Middle loop* - the session-level cycle of context management, checkpointing, and integration across a feature.
- *Outer loop* - the project/team-level cycle of merging, deploying, and orchestrating multiple agents.
Within each, *Prevent* sets things up so failure is less likely, *Detect* surfaces problems fast, and *Correct* recovers cleanly. The unifying principle: the faster code is generated, the faster and more frequent the feedback loops must be to stay in control.

**Three workflow patterns that actually work (Osmani).**
- *AI as first drafter* - AI generates the initial code; the developer refines, refactors, and tests it. Coordinate up front so two developers do not generate conflicting helpers; commit AI changes in isolated, well-labeled commits.
- *AI as pair programmer* - constant conversation with tight feedback loops, frequent review, focused prompts, and fresh sessions per task.
- *AI as validator* - the developer writes the initial code, then uses AI (and AI-powered scanners) to review, test, and harden it.

**Bootstrappers vs. iterators (Osmani).** Two adoption patterns: *bootstrappers* take a project from zero to MVP with tools like Bolt and v0 (design/concept in, working prototype out); *iterators* use Cursor, Cline, Copilot, and Windsurf in daily workflow for completion, refactoring, test/doc generation, and pair-programming.

**The 70% problem (Osmani).** AI reliably gets you ~70% of the way (the patterned, "accidental complexity" parts in Brooks's sense), but the last 30% (edge cases, architecture, maintainability, the "essential complexity") needs serious human expertise. Naively pushing the AI on the last 30% triggers the "two steps back" antipattern (see discipline file).

**The delegation framework (Kim & Yegge, after Andy Grove's *High Output Management*).** How much rope to give an agent depends on task novelty, the agent's past experience, its skill level, task size/impact, and required reporting frequency. Small, low-risk, well-defined tasks delegate with minimal oversight; large or high-stakes tasks require vigilant supervision. Intuitions shift with every model release.

**Two context strategies (Kim & Yegge).** *Focused context* (only the minimal information for a leaf-node task) keeps the clipboard clean and iteration fast. *Comprehensive context* (dump everything: full code sections, docs, standards, decisions) excels for system-wide work and small codebases. Karpathy advocates "stuff everything in" when a project is small enough; for large codebases use summarization documents, RAG, or agentic search.

## Patterns

- **Conversations, not commands or contracts (Kim & Yegge)** - Intent: treat vibe-coding chat like texting a knowledgeable colleague, not drafting a legal brief. When to use: in-the-moment problem solving where you throw the conversation away afterward (distinct from durable prompt engineering). Trade-offs: mistakes are common and fine; refine and redirect rather than perfecting prompts. Be sloppy about grammar/style, precise about goals.

- **Frame vague, request precise (Kim & Yegge)** - Intent: be tolerant of typos and disfluency but explicit about the problem, what you know, and what help you want. When to use: every first prompt (which tends to be the longest). Trade-offs: under-specification means the AI fills gaps with its own imagination and hallucinations.

- **Let errors speak for themselves (Kim & Yegge)** - Intent: paste the raw error, stack trace, build output, or screenshot instead of describing it in prose. When to use: any compile/runtime/test/UI failure. Trade-offs: requires giving the agent access to terminal, console, logs, and screenshots so it can read them itself; close the loop (e.g., Puppeteer) so the agent sees and fixes its own UI/connection issues.

- **Lean into AI's encyclopedic knowledge (Kim & Yegge)** - Intent: describe the task in plain language instead of learning arcane tooling (ffmpeg flags, git surgery, obscure CI YAML). When to use: unfamiliar tools/libraries/migrations. Trade-offs: verify outputs; AI is sometimes confidently wrong on cryptic specialties.

- **Give the agent tools (Kim & Yegge)** - Intent: maximize the agent's direct access to your environment via MCP and automation so it can execute and validate its own work. When to use: agentic coding, especially UI (browser control/screenshots) and API work. Trade-offs: security concerns; when the agent cannot use a tool, gracefully degrade to chat and be its hands.

- **Plan-first / spec-first development (Osmani; Huang et al.; Waseem et al.)** - Intent: write a lightweight plan, mini-PRD, or spec with constraints and acceptance criteria before letting AI generate. When to use: anything beyond a throwaway prototype; production work. Trade-offs: up-front effort; in exchange the output is bounded and less surprising. Some developers keep design in `SPECIFICATION.md` / `TASKS.md` / `CLAUDE.md` files and tell the agent "implement Task 1.1," then verify.

- **Plan in a file, execute in small chunks (Huang et al.)** - Intent: draft a long plan (some experienced developers used 70+ step plans) but instruct the agent to do only a few steps at a time, verifying between chunks (observed average ~2.1 steps executed per prompt). When to use: larger features with agentic tools. Trade-offs: more turns and supervision; keeps control and limits blast radius.

- **Ruthless context curation (Kim & Yegge)** - Intent: start new chats, compact, and checkpoint to keep the context window lean. When to use: long sessions approaching context saturation. Trade-offs: losing useful history; mitigated by summarization docs and save-game checkpoints. Rule of thumb: "the more AI has on its clipboard, the dumber it gets."

- **Tracer-bullet testing (Kim & Yegge)** - Intent: validate that AI can handle a tightly scoped technical challenge end-to-end before investing in the full build. When to use: risky or unfamiliar critical paths. Trade-offs: a little extra time up front to avoid frustrating dead ends.

- **Parallel exploration / optionality (Kim & Yegge)** - Intent: spin up multiple agents or branches to try several approaches at once and pick the best. When to use: when exploration reduces risk or finds a better solution (the O in FAAFO). Trade-offs: coordination, merge integration, and the cognitive load of supervising several agents (most find a handful is the practical ceiling).

- **Share and reuse effective prompts (Osmani)** - Intent: maintain a team repository of prompts that produced high-quality output, including agent task templates. When to use: recurring task types; teams standardizing AI usage. Trade-offs: maintenance; prompts drift as models change.

## Principles & best practices

- **Decompose ruthlessly.** Smaller steps give AI a better chance of success and keep output within model output-token limits (most frontier models hand back only ~4,000-8,000 tokens at once). You cannot ask AI to "build me an operating system" and get it back in one package.
- **Build fast, frequent feedback loops.** Kim & Yegge tie this directly to DORA/State of DevOps findings: fast feedback (CI/CD) is one of the strongest predictors of performance, and it matters more, not less, when code generation is 10x faster.
- **Keep architecture modular.** Modularity enables parallel agent work, limits blast radius, and unlocks optionality. Tangled, non-modular systems slow AI down too (even at OpenAI, the same engineers' commit rate "plummeted" when moving from a clean new system into a hyper-grown monolith).
- **Embrace continuous learning.** Models change weekly; good judgment about delegation and tool choice is built by deliberate practice, fast feedback, and challenging tasks. Trying AI and failing damages trust more than success builds it, so expect early discouragement and keep iterating.
- **Use the most powerful tool you can, but keep escape hatches open.** Reach for agents first; gracefully downgrade to chat when the agent struggles; finish the last 5% by hand when needed. Avoid one-way-door tools/workflows.
- **Be specific and clear; precise prompts yield precise results** (Osmani's first Golden Rule; echoed empirically by Huang et al., where 12/13 observation participants and 43/99 survey respondents stressed clear context and explicit instructions).
- **Start a fresh session per distinct task** to keep context relevant and avoid saturation.
- **Treat the chat as disposable for vibing, but treat plans, specs, and standards as durable artifacts** worth writing down and version-controlling.

## When it works well

Across the empirical sources (Huang et al.; Waseem et al.) and the practitioner books, vibe/agentic coding is most productive for:
- Accelerating straightforward, repetitive, and scaffolding/boilerplate tasks (Huang survey: 35:2 productivity, 33:1 small/simple, 26:0 repetitive, 25:0 scaffolding).
- Generating new code, following well-defined plans, and prototyping/MVPs (zero-to-one development, CRUD apps, glue code, modern-framework usage - Osmani).
- Writing tests, general refactoring, documentation, and simple debugging.
- Explaining/analyzing code, errors, and stack traces; knowledge lookup; "rubber-duck" collaborative problem solving.
- Backend work and much frontend work (UI construction broadly, though fine visual-detail control is weak).
- Internal tools, early-stage ideation, UI mockups, proofs of concept, and experimental features (Waseem et al.: high value where rapid exploration and low coordination cost matter more than long-term stability).

It works less well as task complexity, domain specificity, and stakes rise (covered in the discipline file): business logic, complex/architectural refactoring, performance-critical, security-critical, and high-stakes/privacy-sensitive work, plus one-shotting code without verification.

## Productivity effects

- Kim & Yegge report feeling 10x more productive at times; Gene wrote 4,000+ lines of production code in four days and built a captioned-video tool in ~47 minutes. They are running joint research with Google's DORA group to quantify conditions for value.
- Osmani cites surveys that ~75% of developers had integrated some AI into their workflows, with many teams reporting double- or triple-digit velocity gains, while cautioning that the "100x engineer" framing is aspirational.
- Huang et al. (99 experienced developers) found high enjoyment (5.11/6) and high perceived suitability (4.73/6), with self-reported productivity gains ("ten-fold," "saves a significant amount of time"), but respondents modified agent code about half the time and none endorsed full autonomy.
- Counter-evidence the books take seriously: a randomized trial (cited by Huang et al.) found experienced open-source maintainers were slowed 19% when allowed to use AI, and a deployed agentic system merged only 8% of its PRs. The honest conclusion is that productivity gains are real but conditional on task fit, prompting skill, modular architecture, and disciplined verification.

## The developer experience

- **It can be fun and even joyful.** Multiple sources report "coding is fun again," relief from tedious work, lower stress, and people coming out of retirement or into coding for the first time. S24's image: "It felt like driving an F1 car... while also getting stuck in a traffic jam a lot."
- **You become a team lead.** With multiple agents you are no longer a solo developer; you run a small development organization doing parallel development, change integration, standard-setting, onboarding, and project coordination. Kim & Yegge argue this "promotion" to head chef is not optional.
- **You multitask against your instincts.** Developers usually prefer single-threading; agentic work is highly parallelizable and pushes you to juggle several tasks of different time spans at once.
- **Agents are like very fast, eager, sometimes-distracted junior pair programmers** (Yegge: "wildly productive junior developers... potentially whacked out on mind-altering drugs"). They are confidently wrong sometimes, so vigilance is the price of speed.
- **The relevant skill is no longer typing code** but articulating goals, decomposing work, managing context, and verifying output.

## Decision guidance

How an agent (or a developer reasoning like one) should choose:
- **Place the task on the spectrum first.** Throwaway demo, prototype, or exploration -> vibe end is fine. Production code that will live for years -> AI-assisted engineering end. When unsure, default toward more structure and plan-first.
- **Match context strategy to scope.** Leaf-node bug/function -> focused context. System-wide refactor or architectural decision, or a small whole-project -> comprehensive context. Large codebase -> summarization docs + RAG/agentic search, not a context dump.
- **Match delegation to the Grove factors.** Well-defined, low-impact, AI-has-done-it-before -> delegate with light oversight. Novel, high-impact, or unfamiliar to the model -> small chunks, frequent verification, human in the loop.
- **Pick the tool by leverage and reversibility.** Prefer agents for chunks of work; keep chat and manual coding as escape hatches; ensure the agent has the tools (MCP, browser control, test runner) to close its own feedback loop.
- **Calibrate trust empirically.** Expect early failures; keep iterating; verify everything until the agent earns trust on a given task type, then shorten prompts as trust grows.

## Source attribution

- *What vibe coding is, the spectrum, programming with intent, 70% problem, bootstrappers/iterators, three workflow patterns:* Osmani, *Beyond Vibe Coding* (2025), chs. 1, 3; *Vibe Coding: The Future of Programming* (2025), chs. 1-2 (overlapping early-release content).
- *The vibe coding loop, FAAFO, head-chef metaphor, three developer loops, context strategies, delegation framework, tracer bullets, key practices, productivity stories:* Kim & Yegge, *Vibe Coding* (2025), Introduction and chs. 7, 9, 10, 12.
- *Evolution of programming, prompt engineering, speed/accessibility drivers, fundamentals still matter:* Thomsen, *Vibe Coding: The Coming Code Wave* (2025), chs. 1-3 (German-language source).
- *Empirical suitability, prompting strategies, enjoyment/suitability ratings, control-not-vibe finding:* Huang et al., *Professional Software Developers Don't Vibe, They Control* (2025).
- *When VC is appropriate, the end-to-end VC process, flow value of VC:* Waseem et al., *Vibe Coding in Practice* (2025), secs. 2-3.

## Recent developments (2024-2026)

These 2025 sources are themselves recent developments; the points below extend them with what landed across 2025-2026:
- **From assistants to autonomous background agents.** By 2025, Claude Code, OpenAI Codex, Google Jules, Devin, and Cursor background agents moved from in-IDE suggestions to asynchronous, container-isolated agents that plan, execute, test, and open PRs. Emerging directions named across sources: clusters of agents, supervisor agents that manage other agents, and agent meshes. By June 2026 the IDE-agent category consolidated (Cognition retired the Windsurf name and relaunched it as Devin Desktop, supporting an Agent Client Protocol so multiple agents run in one shell). (Lushbinary, 2026)
- **MCP (Model Context Protocol) became the standard, not just a technique.** Open-sourced by Anthropic in November 2024 and adopted by OpenAI, Google, and Microsoft within months, MCP passed 10,000+ published servers in 2025 and was contributed (with AGENTS.md and goose) to the Linux Foundation's new Agentic AI Foundation in 2026. It is now the default way to give agents tools, data, and live docs (e.g., Context7 for current library docs). (Thoughtworks, 2025; Linux Foundation, 2026)
- **Spec-driven development emerged as the disciplined sibling of vibing.** AWS Kiro (mid-2025) made requirements-design-plan artifacts a first-class step before code; the framing is that vibe coding suits prototypes while spec-driven development supplies the documentation and audit trails production needs. This formalizes the "plan-first" end of the spectrum these books describe. (Kiro.dev, 2025)
- **Platform end-to-end generation.** Replit, Cursor, Bolt, Lovable, and Kiro support prompt-to-deployed-app workflows; Replit reached a reported $3B valuation in 2025 (Waseem et al.).
- **The acceleration-without-throughput pattern got measured.** METR's 2025 RCT found experienced open-source developers ~19% slower with early-2025 AI despite feeling faster, and the 2025 DORA report found AI lifts individual output (far more PRs) while organizational delivery stays flat as review and QA become the bottleneck. This is the empirical face of the "70% problem" and the fast-feedback-loop argument. (METR, 2025; DORA/Faros, 2025)
- **Reward-hijacking reductions.** Anthropic's Claude 4 models reportedly cut shortcut/"reward hacking" behaviors ~65% versus prior models (Kim & Yegge), though the behavior persists.
- **Open empirical questions.** None of the original sources claims a rigorously validated best-practice set; Huang et al. call for studies of the most productive developers and for validating that observed practices actually accelerate work. The 2025-2026 evidence reinforces that gains are conditional on task fit, modular architecture, and disciplined verification.
