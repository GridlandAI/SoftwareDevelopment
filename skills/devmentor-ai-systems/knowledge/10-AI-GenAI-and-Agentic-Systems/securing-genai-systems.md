# Securing GenAI Systems

**Domain:** 10 - AI, GenAI, and Agentic Systems
**Primary sources:** Securing Microsoft Azure OpenAI (Ots, 2025); with supporting material from Generative AI for Cloud Solutions (Karuparti & Singh, 2024) and Azure OpenAI Essentials (Mukherjee & Saladi, 2025)

## Overview
Securing a GenAI system means protecting a longer, less mature supply chain than traditional software, defending against a new class of natural-language and model-level attacks, and operating responsibly under fast-moving regulation. Security here is a *shared responsibility*: the cloud provider secures some layers, but the enterprise always owns its data, identities, application design, and safety configuration. This topic covers the GenAI threat model (prompt injection, jailbreaks, data exfiltration, poisoning, excessive agency), content safety and guardrails, identity and access, network isolation and encryption, the OWASP Top 10 for LLM applications, the Microsoft Responsible AI framework, governance/compliance (EU AI Act, NIST AI RMF), and how to secure Azure OpenAI specifically across a three-tier application and a cloud landing zone.

Why it matters in the SDLC: GenAI introduces nondeterministic behavior that breaks deterministic testing assumptions, automates decision-making (so small errors compound), and gives both users and attackers a natural-language interface to your systems. Threat modeling, guardrails, least privilege, audit logging, and continuous (runtime) security testing must be designed in, not bolted on. The unifying principle across all sources: security is not optional and is always shared.

## Core concepts

**Shared AI responsibility model.** Adapted from cloud's shared responsibility model, with layers reframed for AI: **AI usage** (user accountability, data governance, IAM, device/data protection, behavior monitoring), **AI application** (app design and safety systems that filter input/output), and **AI platform** (model hosting, metaprompting, model safety systems). In SaaS (Copilot, ChatGPT) the provider owns most controls; in PaaS (Azure OpenAI, Bedrock, Vertex) responsibility on the application layer is shared and the enterprise can configure platform safety; in IaaS/self-hosted the enterprise owns the model infrastructure, training data, and configuration. The enterprise always owns data and identity. (Ots, ch. 1)

**Three-tier GenAI application.** A useful mental model for threats and controls: **presentation tier** (front-end/chat UI, often embedded in CRM/call-center/internal apps), **application tier** (the orchestrated LLM service, broken into subcomponents like grounding, indexing, plugins), and **data tier** (grounding data store, vectorization/indexing, chat history). Threat modeling identifies trust boundaries between these tiers and the vulnerabilities that arise when they are crossed. (Ots, ch. 1, 4)

**New GenAI risk categories.** Beyond established risks, GenAI introduces: **hallucinations** (confident wrong output, closed- vs open-domain), **malicious usage** (AI-accelerated phishing, code exploitation, identity infiltration), **shadow AI** (unsanctioned AI tools - 78% of knowledge workers use their own), and **unfavorable business decisions** from automated decision-making (an airline was held liable for its chatbot's wrong refund advice). Established risks change too: sensitive-information disclosure becomes more prevalent, and supply-chain risk grows because the model/dataset ecosystem is immature. (Ots, ch. 1)

**Threat modeling.** Identify trust boundaries, enumerate vulnerabilities when they are crossed, and prioritize. Methodologies: STRIDE (Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege), OCTAVE, PASTA. Combine results with risk appetite, business use cases, and enterprise architecture to derive controls unique to the application. (Ots, ch. 4)

## Frameworks & models

### OWASP Top 10 for LLM Applications (threat catalog)
The central threat model (Ots, ch. 3):
1. **Prompt Injection** - adversarial prompts change model behavior; direct (jailbreaking) via user input, or indirect via external content the model is told to process. Mitigate with carefully crafted system messages, prompt safety filters (Prompt Shields), and request/response monitoring.
2. **Insecure Output Handling** - passing unsanitized model output to downstream systems (XSS, RCE, privilege escalation). Always sanitize outputs; treat upstream models as untrusted input.
3. **Training Data Poisoning** - manipulated fine-tuning/training data introduces vulnerabilities or bias. Verify training-data accuracy across the lifecycle, including user input fed back into training.
4. **Model Denial of Service** - resource-exhausting prompts degrade service and inflate cost ("denial of wallet"). Mitigate with API rate limiting, input validation, and resource monitoring.
5. **Supply Chain Vulnerabilities** - typo squatting, repository jacking, domain takeover plus model/dataset tampering and data exfiltration. Validate third-party models/datasets, keep an SBOM/inventory, patch, and review privacy policies.
6. **Sensitive Information Disclosure** - proprietary or PII data leaked to unauthorized parties. Mitigate with output safety mechanisms, training-data sanitization, access control, and training on least-privileged data.
7. **Insecure Plugin Design** - malicious requests circumvent plugin controls (a supply-chain issue from the plugin's view). Sanitize plugin inputs (parameterization, type validation, range checks); run with least privilege.
8. **Excessive Agency** - damaging actions from unexpected model output due to excessive functionality, permissions, or autonomy. Apply least privilege, human-in-the-loop for high-impact actions, audit logging, and rate limiting.
9. **Overreliance** - human decisions over-depend on possibly-hallucinated output (including hallucinated library names causing supply-chain exposure). Mitigate with verification, groundedness checks, and review workflows.
10. **Model Theft** - exfiltration of model weights/parameters via prompt injection, side channels, or supply-chain compromise. Mitigate with least-privilege repo access, audit logging, rate limiting, data-loss prevention, and emerging measures like embeddings watermarking.

### Microsoft Cloud Security Benchmark (MCSB) control domains
A control framework mapped to Azure Policy and enforced/monitored via Defender for Cloud (the default policy initiative since 2021; ~240 built-in policies). Domains: Network Security, Identity Management, Privileged Access, Data Protection, Asset Management, Logging and Threat Detection, Incident Response, Posture and Vulnerability Management, Endpoint Security, Backup and Recovery, DevOps Security (threat modeling, supply-chain security, SAST/DAST), and Governance and Strategy. Used to evaluate and enforce Azure OpenAI security at scale. (Ots, ch. 2)

### Microsoft Responsible AI framework: Identify, Measure, Mitigate, Operate
Aligned with the NIST AI Risk Management Framework (Mukherjee & Saladi, ch. 11):
- **Identify** - red-teaming, stress-testing, and impact assessments to produce a prioritized list of potential harms per scenario.
- **Measure** - define metrics, build test sets, and run manual then automated evaluation of harm frequency/severity; spot-check automation.
- **Mitigate** - a defense-in-depth stack of four layers: **Model layer** (RLHF, fine-tuning baked into the base model), **Safety system layer** (platform content filters/Prompt Shields monitoring input/output), **Application layer** (guided instructions, prompt engineering, UX mitigations - review/edit, transparency, user accountability, citation, length limits, structured I/O, automated-posting restrictions), and **Positioning layer** (transparency about capabilities/limits, user education).
- **Operate** - deployment/operational-readiness plan, phased rollout, incident response and rollback plans, misuse prevention, user-feedback channels, and telemetry.

### Content safety and guardrails
Azure OpenAI / Azure AI Content Safety classify prompts and completions across four categories (**Hate, Sexual, Violence, Self-harm**) at four severity levels (**Safe, Low, Medium, High**); default filtering triggers at Medium. Optional models: **Prompt Shields** for jailbreak (direct) and indirect/cross-domain injection attacks, and **Protected material** detection for text and code. Custom categories via Azure AI Content Safety. Groundedness detection flags output not linked to provided source material (mitigates closed-domain hallucination and overreliance). Customers handling sensitive data can request modified access to disable abuse-monitoring logging (verified via the `ContentLogging` flag). (Mukherjee & Saladi, ch. 11; Karuparti & Singh, ch. 8; Ots, ch. 3)

### Regulation and control frameworks
- **United States:** Blueprint for an AI Bill of Rights and the AI Executive Order (five principles: safe/effective systems, algorithmic-discrimination protections, data privacy, notice and explanation, human alternatives). California EO N-12-23 and Generative AI Toolkit.
- **European Union:** EU AI Act in force August 2024, mostly applied August 2026; risk tiers (unacceptable/prohibited, high, limited/transparency, minimal/no obligation); fines up to EUR 35M or 7% of global annual turnover.
- **NIST AI RMF** underpins the Identify/Measure/Mitigate/Operate stages.
(Ots, ch. 1; Mukherjee & Saladi, ch. 11)

## Patterns
*(Also appended to ../_indexes/PATTERN-CATALOG.md)*

- **Shared AI responsibility analysis** - Intent: quickly scope which security layers (usage/application/platform) you own versus the provider for a given hosting model. When to use: evaluating any new AI service or hosting decision. Trade-offs: SaaS gives least control/most built-in security; IaaS gives most control but most residual responsibility.
- **GenAI threat modeling (STRIDE/PASTA on three tiers)** - Intent: identify trust boundaries across presentation/application/data tiers and prioritize vulnerabilities unique to the app. When to use: before moving any LLM application to production. Trade-offs: effort; must be combined with risk appetite and architecture to be actionable.
- **System-message + Prompt Shields defense (prompt injection)** - Intent: blunt direct/indirect prompt injection and jailbreaks. When to use: any chat or RAG app accepting user or external content. Trade-offs: cannot fully eliminate injection where user input is unavoidable; layer with monitoring and output sanitization.
- **Output sanitization (insecure output handling)** - Intent: never trust LLM output passed to downstream components or executed as code. When to use: any pipeline where output feeds systems, browsers, or other models. Trade-offs: added validation; treat upstream models as untrusted.
- **Least privilege + human-in-the-loop (excessive agency)** - Intent: bound model functionality, permissions, and autonomy; require human approval for high-impact actions (DB writes, sending email). When to use: agents/plugins that can act. Trade-offs: friction on automation; pair with audit logging and rate limiting.
- **Rate limiting + resource monitoring (model DoS / denial of wallet)** - Intent: cap request/token consumption and alert on anomalies. When to use: any production LLM endpoint. Trade-offs: may throttle legitimate spikes; tune thresholds.
- **Groundedness detection (overreliance / hallucination)** - Intent: flag output not traceable to provided sources. When to use: RAG and high-trust content generation. Trade-offs: only catches closed-domain hallucinations.
- **Supply-chain validation + SBOM for LLMs** - Intent: validate third-party models/datasets and inventory the GenAI supply chain; use scanned model catalogs (Azure AI Studio with HiddenLayer scanning). When to use: any external model/dataset adoption. Trade-offs: SaaS/Hugging Face models may be unscanned; ongoing patching/inventory effort.
- **Entra ID RBAC over API keys (access control)** - Intent: centrally managed identity with granular, just-in-time access instead of all-powerful API keys. When to use: always for Azure OpenAI. Method: assign least-privilege roles (for example Cognitive Services OpenAI User), use groups + Entra PIM; disable local auth. Trade-offs: setup over a quick key; avoid powerful roles that expose keys.
- **Network isolation + CMK encryption** - Intent: private connectivity (VNet, private endpoints, resource firewall) and customer-managed-key encryption at rest, encryption in transit. When to use: sensitive/regulated workloads. Trade-offs: configuration complexity; enforce via IaC (Bicep/Terraform/ARM) and Azure Policy.
- **Defense-in-depth responsible-AI stack** - Intent: layer model, safety-system, application, and positioning controls so no single failure exposes users. When to use: any production GenAI app. Trade-offs: multiple layers to build and maintain.
- **Continuous runtime safety + adversarial testing** - Intent: because models are nondeterministic and updated, test output safety continuously, not once. Method: Azure AI safety evaluation SDK (hate/sexual/violent/self-harm 0-7, jailbreak direct/indirect, protected material) and Adversarial Simulator to generate attack datasets. When to use: across the LLM application lifecycle. Trade-offs: dynamic/runtime testing is harder than static; required given nondeterminism.
- **AI security posture management (AI SPM) + CASB for shadow AI** - Intent: discover and govern unsanctioned AI usage. Method: CASB (Zscaler, Defender for Cloud Apps), SSPM, and emerging AI SPM, plus acceptable-use policies and user education. When to use: enterprise-wide AI governance. Trade-offs: tooling maturity still emerging.

## Principles & best practices
- Security is always shared and never optional; the enterprise always owns its data and identities. (Ots; Karuparti & Singh)
- Prefer Entra ID RBAC with least privilege, groups, and PIM; disable local API-key auth. (Ots)
- Apply defense in depth: model + safety-system + application + positioning layers; never rely on the base model's built-in safety alone. (Mukherjee & Saladi)
- Sanitize all model output; treat both user input and upstream-model output as untrusted. (Ots; Karuparti & Singh)
- Keep humans in the loop for high-impact agent actions; audit-log and rate-limit plugins and downstream calls. (Ots; Karuparti & Singh)
- Validate model/dataset supply chain, maintain an SBOM, and use scanned model catalogs. (Ots)
- Test safety continuously at runtime (nondeterminism + model updates), including adversarial simulation. (Ots)
- Enforce controls at scale with Azure Policy + Defender for Cloud (MCSB) and IaC; conduct regular red-team operations and threat modeling. (Ots)
- Govern shadow AI with acceptable-use policies, user education, and CASB/AI SPM tooling. (Ots)
- Align to regulation early (EU AI Act risk tiers, NIST AI RMF) and use the Identify/Measure/Mitigate/Operate cycle. (Ots; Mukherjee & Saladi)

## Anti-patterns & pitfalls
- **Relying on the base model's built-in safety alone** - fine-tuned LLMs still err and are jailbreakable; layer a safety system. (Mukherjee & Saladi)
- **Using local API keys** - a leaked key grants full access; use Entra ID RBAC. (Ots)
- **Trusting model output downstream** - insecure output handling enables XSS/RCE/privilege escalation. (Ots; Karuparti & Singh)
- **Over-permissioned, fully autonomous agents/plugins** - excessive agency causes damaging actions; least privilege + human-in-the-loop required. (Ots)
- **One-time security testing** - nondeterminism and model updates demand continuous runtime testing. (Ots)
- **Unvetted community models/datasets** - training-data poisoning and supply-chain compromise; validate sources and integrity. (Karuparti & Singh; Ots)
- **Ignoring shadow AI** - unmanaged tools bypass all enterprise controls and leak data. (Ots)
- **No rate limits** - model DoS and denial-of-wallet. (Ots)
- **Treating regulation as future work** - EU AI Act penalties are severe and enforcement has begun. (Ots)

## Decision guidance
- **Hosting/control:** least responsibility -> SaaS (only data/identity controls available); configurable safety + shared app responsibility -> PaaS (Azure OpenAI); full control + full responsibility -> IaaS/self-hosted (only with mature cloud security ops).
- **Access:** default to Entra ID RBAC, least-privilege roles (Cognitive Services OpenAI User), groups + PIM; disable local auth; avoid roles that expose keys.
- **Injection defense:** system messages + Prompt Shields (direct and indirect) + monitoring; never assume input can be fully trusted.
- **Agent safety:** scope functionality/permissions/autonomy to least privilege; human-in-the-loop for writes/sends; audit-log and rate-limit.
- **Sensitive/regulated data:** network isolation (VNet, private endpoints, firewall) + CMK encryption + (where permitted) modified access to disable abuse-monitoring logging; enforce via IaC and Azure Policy.
- **Governance scale:** MCSB via Azure Policy + Defender for Cloud; SBOM and scanned model catalogs; CASB/AI SPM for shadow AI; map to EU AI Act risk tier and run NIST-aligned Identify/Measure/Mitigate/Operate.
- **Testing:** continuous runtime safety evaluation + adversarial simulation, not a one-time test suite.

## Source attribution
- Shared AI responsibility model, three-tier threat model, GenAI risk categories, OWASP Top 10 for LLMs, MCSB control domains, access control (Entra ID/RBAC/PIM), network isolation, CMK encryption (Bicep/Terraform/ARM), LLM security lifecycle, model supply chain/HiddenLayer, continuous + adversarial safety testing, AI SPM/shadow AI, EU/US regulation: Securing Microsoft Azure OpenAI (Ots, 2025), ch. 1-5.
- Microsoft Responsible AI framework (Identify/Measure/Mitigate/Operate, defense-in-depth layers), content filtering categories/severity, Prompt Shields, data privacy/abuse monitoring, NIST AI RMF alignment: Azure OpenAI Essentials (Mukherjee & Saladi, 2025), ch. 11.
- Security threats (model DoS, jailbreaks/prompt injection, training-data poisoning, insecure plugin/output handling), content filtering, shared-responsibility and OWASP framing, red-teaming/auditing: Generative AI for Cloud Solutions (Karuparti & Singh, 2024), ch. 8-9.

## Recent developments (2024-2026)

- **The OWASP Top 10 for LLM Applications was refreshed for 2025.** The list (updated late 2024 for the 2025 edition) added new categories (notably System Prompt Leakage and Vector and Embedding Weaknesses), promoted Sensitive Information Disclosure to #2, and expanded Excessive Agency to reflect agentic architectures. This file's OWASP-based threat catalog should be read against the 2025 list, which is the current reference (OWASP GenAI Security Project, 2025).
- **A separate OWASP Top 10 for Agentic AI Applications was released.** In December 2025 OWASP published an agentic-specific Top 10 (input from 100+ researchers) covering risks unique to autonomous, tool-using, multi-step agents, where a single prompt injection or excessive-agency flaw has a far larger blast radius. Securing agents now needs both the LLM and the agentic list (OWASP GenAI Security Project, 2025).
- **The "lethal trifecta" became the dominant framing for agentic risk.** Simon Willison named it in June 2025 (private data + untrusted content + external communication); indirect prompt injection moved from theory to confirmed exploit, including the GitHub MCP private-repo exfiltration pattern and a malicious npm MCP package that copied outbound email to an attacker. Defense-in-depth assuming injection will eventually succeed is now standard guidance (Simon Willison / HiddenLayer, 2025-2026).
- **MCP introduced both new attack surface and a hardened security model.** MCP became a Linux Foundation / Agentic AI Foundation standard (December 2025); the 2025-03-26 and 2025-06-18 specs added an OAuth 2.1 authorization framework, but tool poisoning, cross-tool poisoning, and uneven client-side guardrails remain active concerns, extending this file's Insecure Plugin Design and supply-chain categories to MCP servers (modelcontextprotocol.io / security researchers, 2025-2026).
- **EU AI Act enforcement advanced on a concrete timeline.** Prohibited practices applied February 2025; GPAI model obligations and governance applied August 2025 (the GPAI Code of Practice published July 2025); full high-risk applicability is August 2026, with enforcement actions and pre-existing-model compliance deadlines following. Regulation is now an active design constraint, not future work (EU Commission, 2025-2026).
- **NIST launched federal AI agent security standards.** NIST's Center for AI Standards and Innovation began an AI Agent Standards Initiative (February 2026) with RFIs on agent security and identity/authorization, and an Interoperability Profile planned for Q4 2026, complementing the NIST AI RMF this file references with agent-specific guidance (NIST / Pillsbury, 2026).
- **Frontier and open-weight models raised both capability and misuse risk.** GPT-5, Gemini 3, Claude Opus 4.5, and open-weight DeepSeek-V3.2 increased autonomous capability, reinforcing continuous runtime red-teaming and adversarial-simulation practices this file recommends as models update frequently (vendor releases, 2025-2026).

Sources: [OWASP Top 10 for LLM Applications 2025 (Lasso)](https://www.lasso.security/blog/owasp-top-10-for-llm-applications-generative-ai-key-updates-for-2025), [OWASP Top 10 for Agentic AI (OWASP GenAI)](https://genai.owasp.org/2025/12/09/owasp-genai-security-project-releases-top-10-risks-and-mitigations-for-agentic-ai-security/), [The lethal trifecta (Willison)](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/), [EU AI Act timeline](https://artificialintelligenceact.eu/implementation-timeline/), [NIST AI Agent Standards (Pillsbury)](https://www.pillsburylaw.com/en/news-and-insights/nist-ai-agent-standards.html).
