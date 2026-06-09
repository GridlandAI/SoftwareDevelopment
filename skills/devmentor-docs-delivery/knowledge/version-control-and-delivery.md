# Version Control and Delivery

**Domain:** 15 - Documentation and Delivery Practices
**Primary sources:** Ultimate Git and GitHub for Modern Software Development (Mishra, 2024); Code, Document, Control (Wieteska, 2025)

## Overview
Version control is the foundation of modern software delivery. Git, a distributed
version control system, lets every developer keep a full history of a project,
work in isolation on branches, and merge changes back without losing work.
GitHub layers collaboration on top of Git - shared repositories, forks, pull
requests, code review, and automation through GitHub Actions - turning version
control into the hub of a team's delivery workflow. Together they let distributed
teams work on their own schedules and integrate their work reliably.

Delivery practice extends from "how do we manage changes to the code" to "how do
we get those changes safely into production." The branching strategy a team
chooses, the way it reviews pull requests, and the degree to which it automates
build, test, and deployment (CI/CD, and its GitOps variant) together determine how
fast and how safely the team ships. Reproducible delivery also rests on humble but
essential foundations: scripting (Bash) to automate repeatable tasks, plain-text
artifacts under version control, and disciplined commit hygiene.

## Core concepts

**Repository, commit, branch.** A *repository* holds a project and its full
history. A *commit* is an atomic snapshot of changes with a message. A *branch* is
a parallel line of development that isolates work from the main line until it is
merged.

**Distributed model.** Every clone is a complete copy with full history. This is
what lets team members across time zones work locally and independently, then
integrate through a shared remote (typically GitHub).

**Merge vs rebase.** *Merge* combines two branches, preserving both histories and
creating a merge commit (non-destructive, keeps context). *Rebase* replays one
branch's commits on top of another, producing a linear history (cleaner log, but
rewrites history - use carefully, never on shared/public branches).

**Fork -> branch -> pull request -> review -> merge.** The canonical GitHub
collaboration loop: fork (or branch) to get an isolated copy, make focused changes,
open a pull request to propose them, have peers review, then merge once approved
and checks pass.

**Pull request (PR).** A proposal to integrate a branch, plus the conversation
around it. It is an invitation to collaboration and review, not just a code drop.

**CI/CD.** *Continuous integration* runs automated builds and tests on every
change; *continuous delivery/deployment* automates getting passing changes toward
or into production. On GitHub this is implemented with **GitHub Actions** workflows
defined in `.github/workflows/*.yml`, triggered by events like a push or PR.

**GitOps.** An operations model where Git is the single source of truth for both
application and infrastructure. Desired state lives as code in Git; changes go
through pull requests; an automated pipeline reconciles the running system to the
committed state. It is not a replacement for DevOps but an application of its
principles to infrastructure and deployment.

## Frameworks & models

### Branching strategies
- **Feature-branch workflow** - each piece of work gets its own short-lived branch,
  merged back via PR. The default collaborative pattern in the book; isolates work
  so it can be reviewed independently.
- **GitHub Flow** - a lightweight model: branch off `main`, commit, open a PR,
  review, merge, deploy. Suits continuous delivery with frequent small releases.
- **Git Flow** - a heavier model with long-lived `main` and `develop` branches plus
  `feature`, `release`, and `hotfix` branches. Suits scheduled releases and
  multiple versions in flight; the book cautions against the long-lived branches it
  implies (see pitfalls).
- **Trunk-based development** - developers integrate small changes into a single
  shared trunk (`main`) very frequently, behind feature flags if needed, keeping
  branches extremely short-lived. Minimizes merge pain; demands strong CI and
  review discipline.

The book's strongest branching advice is independent of which model you pick:
**keep branches short-lived** and integrate often, because long-lived branches
diverge and produce painful merge conflicts.

### Pull requests and code review
A good PR:
- Has a **concise, descriptive title** and a **description explaining the what and
  the why**, with links to related issues/tickets for context.
- Is **small and focused** - one issue or one feature. If you find yourself saying
  "and it also does...", split it. Small PRs review faster, integrate cleaner, and
  make issues easier to trace.
- Has been **self-reviewed** line by line before submission.

Effective code review:
- **Critiques the code, not the person**; phrase feedback as improvement ("adding
  error handling here would improve reliability") rather than accusation.
- **Provides context** - explain *why* a change is suggested, turning review into
  shared learning.
- **Reinforces standards** - point out deviations from coding conventions and link
  to them.
- **Balances speed and thoroughness** - timely reviews prevent bottlenecks, but do
  not sacrifice understanding the change's full impact.
- **Is bidirectional learning** - ask questions about unfamiliar approaches; share
  better patterns when you know them.

### Commit hygiene
- **Atomic commits** - one self-contained change per commit. Benefits: easier
  review, simpler troubleshooting (bisect/blame land on the real change),
  straightforward reversion, and a clear history. Achieve with `git add -p`
  (stage selectively), commit frequently, and refine before committing.
- **Effective commit message** - three parts:
  - *Subject* - <=50 chars, imperative mood ("Fix overflow error in chart
    rendering"), capitalized, no trailing period.
  - *Body* - wrapped at 72 chars; explains the *why* and *how*, the problem before
    and the resolution, and alternatives considered.
  - *Footer* - administrative metadata: issue references ("Closes #34"),
    co-authors, breaking-change notes.
- **`.gitignore`** - exclude files that should never be tracked (logs, temp files,
  local config, build output, installed dependencies) using wildcard patterns and
  `#` comments.
- **Keep secrets out of repositories** - never commit credentials or sensitive
  data; history is permanent.

### CI/CD with GitHub Actions
A minimal Actions workflow lives at `.github/workflows/main.yml` and declares:
- a **name**,
- an **`on`** trigger (e.g. `push` to `main`),
- one or more **jobs**, each with a `runs-on` runner and a sequence of `steps`
  (e.g. `actions/checkout`, build, test, deploy commands).

Start small (build + deploy), watch the run in the Actions tab, read logs on
failure, iterate, then expand to run tests, send notifications, and add rollback
capability. Every PR can trigger automated tests so new code cannot break existing
functionality before merge.

### GitOps workflow and principles
Operational loop: propose change via PR -> review/approve -> merge -> automated
pipeline deploys, continuously reconciling running state to the Git state.

Four principles:
1. **Infrastructure as Code (IaC)** - all infrastructure defined as version-
   controlled code, giving consistency, transparency, and repeatability.
2. **Immutable infrastructure** - never modify deployed infrastructure in place;
   change the code, commit, and deploy a new version.
3. **Continuous deployment** - every commit to the main branch triggers automated
   deployment so running state always matches the repository.
4. **Observability and feedback loops** - monitoring/logging detect drift from
   desired state early and trigger correction.

Advantages: enhanced collaboration (familiar PR workflow for ops), improved
reliability (less human error, traceable/reversible changes), faster recovery
(roll back to a known-good commit), simplified compliance/auditability (Git as
audit trail), and streamlined operations.

### Bash and scripting for reproducible delivery (Wieteska)
Reproducible delivery rests on automatable, plain-text foundations:
- **Bash scripting** - automate repetitive tasks with variables, conditionals,
  loops, pipes/redirection, and scheduling via `cron`. Power tools: `grep`, `sed`,
  `awk`, `find`, `xargs`, `tar`, `du`, `chmod`.
- **Script safety** - use `set -euo pipefail`, modularize with functions, and
  always quote variables.
- **The integrated workflow** - Bash to do the work, Markdown to document it
  (README near the code), Git to version and collaborate; this trio is the base
  layer of a reproducible command-line delivery workflow.

## Patterns

- **Feature-branch workflow** - Intent: isolate each unit of work on its own
  branch and integrate via PR. When to use: most team development. Trade-offs:
  branch management overhead; enables independent review and parallel work.
  *(version-control-and-delivery.md)*
- **Short-lived branches / integrate often** - Intent: keep branches alive only
  briefly to avoid divergence. When to use: always, regardless of branching model.
  Trade-offs: requires frequent merging and strong CI; eliminates big-bang merge
  conflicts. *(version-control-and-delivery.md)*
- **Fork-and-pull-request** - Intent: contribute to a repository you do not own
  by forking, changing, and proposing via PR. When to use: open-source and
  cross-team contribution. Trade-offs: extra sync step from upstream; enables safe
  outside contribution. *(version-control-and-delivery.md)*
- **Small, focused pull request** - Intent: one PR addresses one concern so it
  can be reviewed and integrated cleanly. When to use: every change. Trade-offs:
  more PRs to manage; faster reviews, easier tracing, fewer escaped bugs.
  *(version-control-and-delivery.md)*
- **Atomic commit** - Intent: one self-contained change per commit. When to use:
  always. Trade-offs: more frequent committing and selective staging; clean
  history, easy revert and bisect. *(version-control-and-delivery.md)*
- **Conventional commit message (subject/body/footer)** - Intent: a structured,
  imperative-mood message that records the why and links issues. When to use:
  every commit. Trade-offs: discipline to write well; durable, searchable history.
  *(version-control-and-delivery.md)*
- **Code review as collaboration** - Intent: critique the code, give context,
  reinforce standards, and learn both ways. When to use: every PR. Trade-offs:
  reviewer time; higher code quality and shared knowledge. *(version-control-and-delivery.md)*
- **CI on every pull request** - Intent: run automated build/tests on each PR so
  broken changes never merge. When to use: any repository with tests. Trade-offs:
  pipeline maintenance and runner cost; protects `main`. *(version-control-and-delivery.md)*
- **GitOps (Git as single source of truth)** - Intent: drive infrastructure and
  deployment from version-controlled desired state reconciled automatically. When
  to use: cloud-native/IaC environments needing auditability and fast rollback.
  Trade-offs: tooling and cultural shift; strong reliability, recovery, and
  compliance. *(version-control-and-delivery.md)*
- **Infrastructure as Code** - Intent: define infrastructure as versioned code for
  consistency and repeatability. When to use: any non-trivial environment.
  Trade-offs: upfront codification; reproducible, reviewable infrastructure.
  *(version-control-and-delivery.md)*
- **Immutable infrastructure** - Intent: replace rather than mutate deployed
  infrastructure. When to use: with IaC/GitOps pipelines. Trade-offs: rebuild
  cost; eliminates configuration drift. *(version-control-and-delivery.md)*

## Principles & best practices
- **Commit often, in small chunks** - frequent commits act as checkpoints and make
  it easy to find and revert problems.
- **Keep branches short-lived** and merge back quickly; reverse-merge (upmerge)
  from `main` to keep unavoidable long branches current.
- **Write descriptive, consistent commit messages** so history tells a story.
- **Keep PRs small and self-reviewed**; describe the what and the why.
- **Pull before you push** to avoid avoidable conflicts.
- **Automate tests on every change** so regressions are caught before merge.
- **Treat Git as the source of truth** for code and (in GitOps) infrastructure;
  every change is traceable and reversible.
- **Keep secrets out of the repo** and use `.gitignore` to keep it clean.
- **Script for safety and reuse** - `set -euo pipefail`, functions, quoted
  variables.
- **Communicate to prevent conflicts** - stand-ups and coordination reduce the
  same-file collisions that cause most merge conflicts.

## Anti-patterns & pitfalls
- **Long-lived branches** - branches kept apart from `main` for too long diverge
  badly and create a "parallel universe" that is agony to merge.
- **Large, infrequent commits** - obscure history and make bugs hard to locate and
  revert.
- **Vague commit messages** ("fixes", "updates") - force readers to dig through
  the code to learn what changed.
- **Giant pull requests** - mixing many features and fixes; reviewers miss
  problems, bugs slip through, and morale suffers.
- **Reviewing the author, not the code** - accusatory feedback that harms team
  dynamics without improving the code.
- **Rebasing shared branches** - rewriting public history breaks collaborators'
  repositories.
- **Committing secrets** - credentials in history are effectively permanent and a
  security incident.
- **Mutating deployed infrastructure by hand** - drift between environments and
  the "it works on my machine" problem; GitOps/immutable infra solves it.
- **Unsafe scripts** - unquoted variables and no `set -euo pipefail` make
  automation fragile and dangerous.

## Decision guidance
- **Choosing a branching model:** for continuous delivery with frequent small
  releases, prefer GitHub Flow or trunk-based development with strong CI; for
  scheduled releases or multiple maintained versions, Git Flow's structure helps -
  but in all cases keep working branches short-lived.
- **Merge vs rebase:** merge to preserve real history and context on shared
  branches; rebase only to clean up *local, unshared* history into a linear log.
- **PR size:** if a PR spans more than one concern, split it; reviewability and
  traceability beat fewer, larger PRs.
- **How much CI/CD:** start with CI (build + test on every PR) to protect `main`,
  then add automated deployment; adopt GitOps when you manage infrastructure as
  code and need auditability and fast rollback.
- **GitOps vs ad hoc ops:** choose GitOps when traceability, reproducibility, and
  quick recovery matter (regulated industries, frequent deploys, scaling
  infrastructure); the audit trail and rollback are the decisive advantages.
- **Automate or do by hand:** if a delivery task is repeated, script it (Bash) and
  version the script; reproducibility comes from code, not memory.

## Source attribution
- Branching strategies, the fork/PR/review/merge collaboration loop, merge vs
  rebase, advanced techniques (rebase, cherry-pick, stash, bisect), commit message
  anatomy, atomic commits, `.gitignore`, PR etiquette and effective code review,
  GitHub Actions CI/CD pipelines, GitOps principles/advantages/case studies, and
  the long-lived-branch and conflict guidance all come from *Ultimate Git and
  GitHub for Modern Software Development* (Mishra, 2024), Chapters 5-10.
- Bash scripting fundamentals and power tools, script-safety practices
  (`set -euo pipefail`, functions, quoting), the integrated Bash/Markdown/Git
  workflow, basic Git command flow, and the README-near-code practice come from
  *Code, Document, Control* (Wieteska, 2025), Chapters 1, 2, 4, and 5.

## Recent developments (2024-2026)
Git, the GitHub collaboration loop, branching strategies, and CI/CD fundamentals are stable. The 2024-2026 emphasis is AI-assisted delivery, the continued primacy of trunk-based development, and feature-flag-driven safety.

- **Trunk-based development remains the DORA-backed default for high performers.** DORA research continues to show that teams with three or fewer active branches, merging to trunk at least daily, paired with fast automated tests, achieve higher delivery and operational performance. This validates the file's branching guidance. (dora.dev, "Capabilities: Trunk-based development," 2024-2025; atlassian.com, "Trunk-based Development.")
- **DORA's focus shifted to AI-assisted software delivery.** Where the 2024 State of DevOps report centered platform engineering and user-centricity, the 2025 DORA research focuses on how teams adopt AI in the development workflow while protecting reliability and flow; the Four Keys (deployment frequency, lead time, change-fail rate, MTTR) remain the measurement backbone. (dev.to, "What Devs Are Actually Learning in 2025 (Stack Overflow + DORA Insights)," 2025.)
- **GitHub Actions is the default CI/CD for GitHub-hosted teams.** Modern pipeline guidance treats GitHub Actions as the standard automation layer alongside Jenkins, Azure DevOps, and others, with reusable workflows and pipeline-as-code as the norm. (gitnexa.com, "Modern CI/CD Pipelines: 2026 Guide.")
- **Feature flags decouple deploy from release and cut incidents.** Trunk-based delivery increasingly relies on feature flags to merge incomplete work safely and release progressively; 2025 industry findings report large reductions in deployment-related incidents after adopting feature switches (one report cites an 89% drop). (aviator.co, "Managing Continuous Delivery with Trunk-Based Development"; Nudge 2025 research via dev.to, 2025.)
- **AI enters the version-control workflow.** Coding agents now open and review pull requests, draft commit messages, and summarize diffs, making disciplined commit hygiene, small reviewable changes, and clear PR descriptions (already recommended here) more valuable because both humans and agents consume them. (gitnexa.com, 2026.)
