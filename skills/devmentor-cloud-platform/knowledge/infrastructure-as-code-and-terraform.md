# Infrastructure as Code and Terraform

**Domain:** 09 - Cloud Infrastructure and Platform
**Primary sources:** Architecting AWS with Terraform (Kavas, 2024)

## Overview
Infrastructure as Code (IaC) is the practice of provisioning and managing computing infrastructure through machine-readable definition files rather than interactive consoles or manual hardware setup. It applies proven software engineering techniques - version control, code review, testing, modularity - to infrastructure, making it a foundational DevOps practice for delivering infrastructure and software rapidly, repeatably, and at scale. IaC is what makes continuous delivery and reproducible environments possible.

Terraform is the dominant open-source IaC tool. It uses a declarative model: you describe the desired end state of your infrastructure in HashiCorp Configuration Language (HCL), and Terraform computes and executes the steps needed to reach that state. Its provider model abstracts away the differences between cloud platforms, so the same workflow manages AWS, Azure, Google Cloud, VMware, SaaS APIs, and more. This topic captures the durable principles of IaC and the Terraform concepts an architect needs to design reusable, governed, drift-free infrastructure.

## Core concepts

**Declarative vs. imperative.** Imperative approaches specify the exact steps to reach a state ("create this, then configure that"). Declarative IaC (Terraform, CloudFormation) specifies the desired end state and lets the tool resolve dependencies and order of operations. Declarative is preferred for infrastructure because it is idempotent and self-documenting.

**Idempotency.** Applying the same configuration any number of times produces the same end state without unintended changes. This enables safe retries and rollbacks. A stateful tool like Terraform achieves idempotency by tracking actual state and reconciling it against the desired configuration.

**Immutability.** Rather than mutating live infrastructure (which causes configuration drift over time), immutable infrastructure replaces it: build new, switch over, dispose of old. This eliminates "snowflake" servers, guarantees reproducibility, and makes scaling predictable. Manual changes are prohibited; everything flows through code.

**Configuration drift.** Drift occurs when the deployed infrastructure diverges from the code definition, usually because someone made a manual change via the console. Drift causes inconsistent behavior, security gaps, and failed future updates that are hard to reproduce and troubleshoot. Mitigate drift by educating teams against manual changes, restricting console write access in critical environments, and building automated drift detection (both Terraform and CloudFormation support drift detection).

### Terraform architecture (four components)
- **Providers** - plugins that interface between Terraform and a target API (AWS, Azure, GCP, VMware, SaaS). Declared in the root module; child modules inherit them. Each provider has its own release cadence; the dependency lock file pins versions.
- **Resources** - blocks that map to one or more real infrastructure objects (a VPC, an instance, a DNS record). Each provider exposes resources matching its APIs.
- **Modules** - a directory of `.tf` files grouping related resources for reuse. The directory you run commands from is the **root module**; modules it calls are **child modules**. Sourced locally, from the public Terraform Registry (10,000+ modules), VCS, HTTP, or a private registry.
- **Templates** - the collection of configuration files (variables, resources, modules) that define the desired state.

### Terraform workflow (five steps)
1. **Write** - author HCL in an editor; store in version control even when working solo.
2. **Init** (`terraform init`) - initialize the working directory, download providers and child modules, configure the backend, write the dependency lock file. Safe to run repeatedly.
3. **Plan** (`terraform plan`) - read current real state, compare to desired configuration, and produce an execution plan previewing additions/changes/deletions. Does not modify anything; this is the critical human-review gate.
4. **Apply** (`terraform apply`) - execute the plan after confirmation. Safe to run repeatedly because the model is declarative.
5. **Destroy** (`terraform destroy`) - tear down all managed objects. Used for ephemeral/dev environments; used with extreme caution (or `prevent_destroy`) in production.

### State
Terraform maintains a **state file** (JSON) recording the current configuration of managed infrastructure, which lets it know what actions to take on change. State storage options:
- **Local state** (`terraform.tfstate`) - default; simplest but unsuitable for teams.
- **Remote state** - stored in S3, GCS, Azure Blob, or Terraform Cloud; enables team sharing and protects against local data loss.
- **State locking** - with a remote backend, locking prevents concurrent modifications and the resulting conflicts.
State can contain secrets; always use a secure, access-controlled, encrypted backend and avoid writing secrets to state where possible.

### Workspaces
Workspaces let one configuration manage multiple environments (dev, staging, production) with isolated state. Pair them with separate credentials/profiles per environment so a change targets only the intended environment.

## Frameworks & models

**IaC maturity decisions** (from project start). A team adopting IaC must decide: where to store code (Git is the de facto standard - GitHub, GitLab, Bitbucket); how to structure it (mono-repo vs. per-tool vs. per-application/infrastructure-type, plus a branching strategy); how to run it (CI/CD such as Jenkins, GitLab CI, or GitHub Actions with pre-configured agents for tool-version consistency); how to handle secrets (a vault that injects them at runtime, never stored in the repo); a common toolset; the level of pipeline gating (mandatory reviews, manager approval, or self-service); and the infrastructure life cycle (POC scripts need far less rigor than production code).

**Layered modules with governance.** Separate infrastructure into layers (networking, data, compute/EKS, application) as distinct modules and assign ownership of each layer to the responsible team. Versioning modules lets multiple service versions coexist without breaking production. This supports the differing team structures and responsibilities found in real organizations.

**Terraform vs. CloudFormation decision.** Choose **CloudFormation** when the entire estate is AWS-only with no multi-cloud intent: it is AWS-native, gets fastest AWS-centric updates, uses familiar JSON/YAML, has built-in CI/CD with automatic rollback, and assured AWS compliance. Choose **Terraform** when there is any multi-cloud usage (current or future), when faster provider support matters, and when reusable module-based templates are valued. Terraform spans many providers with one workflow; its `plan` is a powerful change-preview; but it has no native rollback (build one in CI/CD), no native RBAC (delegated to the VCS), and requires third-party tooling for compliance.

**GitOps.** Combining IaC with Git workflows enables faster, safer, auditable change rollout: infrastructure changes go through pull requests, CI validation, and approval before merge, and the Git history provides a complete audit trail. Treat IaC code exactly like application code.

## Patterns

**Reusable module** - Intent: encapsulate related resources behind a clean interface so they can be reused, versioned, and governed across projects. When to use: any configuration element repeated across teams/environments (e.g., a hardened S3 bucket module, a standard VPC). Trade-offs: module refactoring is harder than app refactoring (especially for DNS, networking, databases); requires disciplined versioning to avoid breaking consumers.

**Standard module structure** - Intent: consistency and discoverability. Convention: `main.tf` for primary resources, descriptive resource files grouped by purpose (`network.tf`, `instances.tf`, `dns.tf`) rather than one file per resource, a `README.md`, an `examples/` folder with per-example READMEs, and a `docs/` subdirectory. When to use: every shared module. Trade-offs: minor upfront overhead.

**Remote state with locking** - Intent: enable team collaboration and prevent concurrent-write corruption. When to use: any multi-person or production setup. Trade-offs: backend must be secured and access-controlled; introduces a shared dependency.

**Workspace-per-environment** - Intent: manage dev/staging/prod from one configuration with isolated state and credentials. When to use: promoting identical infrastructure across environments. Trade-offs: must guard against applying to the wrong workspace; some prefer separate directories/state for stronger isolation.

**Ephemeral test environment** - Intent: provision a throwaway environment, run integration/smoke/compliance tests, then `terraform destroy`. When to use: validating infrastructure in CI (e.g., assert no S3 bucket is public, only allowed ports open). Trade-offs: time and cost of provisioning; run after commits or as nightly builds depending on duration.

**Prevent-destroy on stateful resources** - Intent: protect databases and other stateful resources from accidental deletion via the `prevent_destroy` lifecycle rule and provider deletion protection. When to use: any production data store. Trade-offs: requires explicit removal of the guard for intentional teardown.

## Principles & best practices

- Keep everything in version control - even the smallest scripts and pipeline configs - for history, ownership, traceability, and team visibility.
- Make infrastructure code accessible across the organization so anyone can understand and troubleshoot how infrastructure is provisioned.
- Prefer reusable, versioned modules; keep changes small, independently deployable, and low-blast-radius.
- Keep documentation close to the code (a `README.md` in the repo, updated in the same commit/PR); generate docs from code or use tests as documentation; outdated docs are worse than none.
- Test at multiple levels: static analysis (`terraform validate`, TFLint), unit tests where conditionals/loops exist, integration/validation tests in ephemeral environments, and smoke tests with a dummy app. Always review `terraform plan` output before `terraform apply`.
- Use `terraform fmt` for consistent formatting; limit expression complexity (use `local` values instead of deeply nested interpolation); use `count` for conditional resources.
- Security and compliance: apply least-privilege RBAC, manage secrets with a vault (HashiCorp Vault, AWS Secrets Manager), run security scans (CIS Benchmarks, Amazon Inspector) in lower/ephemeral environments, and automate compliance checks (Chef InSpec, HashiCorp Sentinel) in the pipeline. Two secret types exist: those used to create resources (admin-only, stored as protected CI variables) and those generated at runtime (must never be logged).
- Use a secure, encrypted remote backend with access controls; commit the dependency lock file so the same provider versions are selected on every init.
- Adopt naming conventions and a common toolset for sustainability and reuse.
- Always pin and version module updates to prevent a small misconfiguration from propagating across many resources; keep tools and code current with cloud provider API/policy changes.

## Anti-patterns & pitfalls

- **Manual console changes** that bypass code, causing configuration drift, outages, and unreproducible state.
- **Storing secrets in repositories** - credentials get copied to every machine that clones the repo. Use a vault instead.
- **Writing tests for what the tool already guarantees** - for declarative code, test outcomes (no public buckets) not that a policy "was applied."
- **One file per resource** - fragments configuration; group by shared purpose instead.
- **Unversioned module updates** - a single change ripples uncontrolled across all consumers.
- **Over-documentation / stale documentation** - more docs is not better; outdated docs mislead.
- **Treating IaC as a side activity** rather than first-class code with approvals, CI validation, and review.
- **Underestimating the adoption curve** - operation-centric teams used to consoles face real resistance; champion automation, security, and compliance actively.
- **No approval flow** - letting changes reach production without review/validation invites misconfiguration; gate merges through CI.

## Decision guidance

- **Tool choice:** AWS-only and want zero learning curve plus built-in rollback → CloudFormation. Multi-cloud now or later, want reusable modules and a strong change-preview → Terraform. Base the decision on infrastructure strategy, security/compliance needs, and cloud adoption strategy.
- **State storage:** solo POC → local state is fine; any team or production → remote backend with locking and encryption.
- **Environment isolation:** workspaces for lightweight multi-environment management; separate state/directories when stronger isolation or blast-radius control is required.
- **Module granularity:** create a module when a configuration is reused or needs governance/consistency (e.g., security-sensitive resources like object storage). Keep modules small and single-purpose.
- **Testing depth:** match rigor to the life cycle - POC scripts need static analysis; production code needs the full pyramid (static, integration in ephemeral environments, smoke, compliance) wired into CI.
- **Promotion to production:** require `plan` review and approval; never merge to production without validation; protect stateful resources with `prevent_destroy`.

## Source attribution

- **IaC definition, idempotency, immutability, drift, IaC patterns/practices (VCS, modules, documentation, testing, security/compliance), IaC project decisions, challenges and best practices, GitOps:** Architecting AWS with Terraform, Ch. 1 "Understanding Patterns and Antipatterns of IaC and Terraform" (Kavas, 2024).
- **Terraform architecture (providers, modules, resources, templates), workflow (write/init/plan/apply/destroy), Terraform vs. CloudFormation comparison:** Architecting AWS with Terraform, Ch. 2 "How Not to Use IaC and Terraform" (2024).
- **State storage (local/remote/locking), backends, workspaces, naming conventions, standard module structure, prevent_destroy, fmt, expression complexity, secure remote state:** Architecting AWS with Terraform, Ch. 3 "Building Your First Terraform Project," Ch. 4 "Discovering Best Practices for Terraform IaC Projects," Ch. 7 "Implementing Terraform in Projects," and Ch. 14 "Building a Secure Infrastructure with AWS Terraform" (2024).

## Recent developments (2024–2026)

- The Terraform licensing fallout reshaped the IaC landscape. After HashiCorp relicensed Terraform from MPL to the Business Source License (BUSL) in August 2023, a coalition (Gruntwork, Spacelift, Harness, Env0, Scalr and others) launched OpenTofu, which shipped its first stable release in January 2024 under Linux Foundation governance. By 2025 OpenTofu had passed roughly 140+ pledged organizations and millions of downloads, with one vendor reporting about half of new deployments on OpenTofu. (OpenTofu, https://opentofu.org/blog/opentofu-announces-fork-of-terraform/; Spacelift, https://spacelift.io/blog/terraform-license-change)
- OpenTofu diverged on features Terraform lacked or added later: state file encryption arrived in OpenTofu v1.7 (April 2024), early-evaluation/variable improvements followed, and releases continued through OpenTofu 1.11 in December 2025. Its multi-vendor TSC governance means no single company can unilaterally change the license again. (OpenTofu FAQ, https://opentofu.org/faq/)
- IBM completed its acquisition of HashiCorp (announced April 2024), folding Terraform, Vault, and the rest of the stack into IBM; the BUSL "Additional Use Grant" still converts affected code to MPL 2.0 four years after each release. (Infisical, https://infisical.com/blog/terraform-vs-opentofu)
- AI-assisted IaC authoring and policy generation became mainstream, and platform-engineering teams increasingly wrap Terraform/OpenTofu behind internal developer platforms and orchestrators (Humanitec, Spacelift, Env0) rather than exposing raw HCL to developers. (platformengineering.org, 2025, https://platformengineering.org/blog/terraform-vs-opentofu-iac-tool)
- On Azure, Microsoft made Bicep-based Azure Verified Modules for Landing Zones generally available (Jan 2026), phasing out the older "classic" ALZ-Bicep approach and standardizing both Bicep and Terraform reference implementations. (ADTmag, 2026, https://adtmag.com/articles/2026/01/20/microsoft-makes-bicep-azure-verified-modules-for-azure-landing-zones-generally-available.aspx)
