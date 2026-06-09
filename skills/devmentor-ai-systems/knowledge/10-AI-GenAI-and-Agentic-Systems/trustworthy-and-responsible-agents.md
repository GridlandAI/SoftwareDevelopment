# Trustworthy and Responsible Agents

**Domain:** 10 - AI, GenAI, and Agentic Systems
**Primary sources:** *Explainable, Trustworthy, and Responsible AI and Multi-Agent Systems* - EXTRAAMAS 2025 proceedings (Calvaresi, Najjar, Omicini, Aydogan, Carli, Ciatto, Tiribelli, Framling, eds., Springer LNAI 15936, 2026); *The Rise of AI Agents: Integration of AI, Blockchain Technologies and Quantum Computing* (Radanliev, Addison-Wesley, 2025).

## Overview

As agents move from research demos to autonomous operation in finance, healthcare, law, and critical infrastructure, their transparency, trustworthiness, accountability, and societal impact become first-order engineering concerns rather than afterthoughts. This file synthesizes two complementary 2025-2026 sources: the EXTRAAMAS 2025 workshop proceedings (the leading venue for explainable, trustworthy, and responsible AI and multi-agent systems) and Radanliev's book on integrating AI agents with blockchain to achieve verifiable, accountable autonomy.

Two strands run through the material. The first is **explainability** - making agent decisions interpretable to humans *and to other agents* - and the ethics of how explanations are delivered (avoiding manipulation, respecting cognitive vulnerability, decolonizing governance). The second is **structural trust** - using cryptographic and distributed-ledger mechanisms (immutable audit trails, smart contracts, smart oracles, post-quantum cryptography) to make autonomous agent decisions tamper-evident, verifiable, and accountable without relying on a central authority. Together they frame how to build agents that are not only capable but transparent, governable, and aligned with human values.

## Core concepts

**Explainable AI (XAI) (EXTRAAMAS).** Techniques developed to bring trust, transparency, and understanding to otherwise black-box AI, supporting safer and more trustworthy systems. XAI techniques are categorized by explanation scope (local vs. global), model dependence (model-agnostic vs. model-specific), and output form. Most XAI work historically targets ML models; the frontier extends it to optimization, agents, and inter-agent settings.

**Inter-agent explainability (EXTRAAMAS).** A shift from the dominant human-targeted view of explainability toward agents that exchange explanations *with each other* to enable better communication and cooperation. Recognized as a potential enabler of useful multi-agent dynamics but historically under-researched, especially in belief-desire-intention (BDI) systems.

**Actionable vs. placebic explanations (EXTRAAMAS, Shymanski et al.).** A placebic explanation feels satisfying but conveys no real information; an actionable explanation genuinely improves the user's mental model. Critically, users rate the two as *equally satisfying*, so subjective satisfaction surveys cannot distinguish meaningful from vacuous explanations. Participants given actionable explanations objectively outperformed others on task understanding despite equal satisfaction ratings.

**Ethical persuasion and manipulation risk (EXTRAAMAS, Bhattacharya et al.).** Anthropomorphic design and persuasive explanations can exploit cognitive vulnerabilities, risking unintended manipulation. Responsible XAI must balance effectiveness against ethical constraints, modeling user vulnerability and bounding persuasion over time.

**Digital trust via blockchain (Radanliev).** Trust historically resided in central institutions (banks, courts, governments). Blockchain relocates trust to a decentralized system: data organized into hash-linked, time-stamped blocks makes records effectively immutable, because changing one block requires re-computing all subsequent blocks across a distributed network. This immutability is the foundation of trust and lets agents operate where no central authority governs trust.

**Smart contracts and chain code (Radanliev).** Self-executing contracts with terms written directly into code (Solidity on Ethereum; chain code on Hyperledger Fabric). They automate and enforce agreements without intermediaries, letting agents transact autonomously when conditions are met.

**Smart oracles (Radanliev).** Oracles bridge on-chain smart contracts to off-chain real-world data. AI-augmented "smart oracles" add sophisticated data analysis for accuracy and anti-manipulation; decentralized oracle networks (Chainlink, Band Protocol, API3) emphasize data integrity through distributed sourcing. Essential where agents must act on timely external data (markets, supply chains, weather-triggered insurance).

**Post-quantum cryptography (Radanliev).** As quantum algorithms (Grover's, Shor's) threaten classical cryptography, NIST-compliant post-quantum schemes (lattice-based, hash-based) protect AI systems and blockchains. Necessary but not sufficient - breaches are expected, so auditability via immutable ledgers remains essential.

## Frameworks & models

**Ladder of Intentions - unifying agent architectures for explainability (EXTRAAMAS, Gimenez-Abalos et al.).** A causality-, belief-, and intention-based framework that examines heterogeneous agents (BDI, RL, LLM-based) *without modification*, providing a common substrate for explaining and comparing decision mechanisms across architectures. Addresses the proliferation of ad hoc, architecture-specific explanation techniques that cannot be compared.

**Engineering inter-agent explainability in BDI agents (EXTRAAMAS, Beaumont et al.).** Surveys BDI explainability, identifies how existing (human-targeted) strategies align with the problem of agents exchanging explanations with each other, and lays a technical roadmap toward implementing inter-agent explainability mechanisms in BDI multi-agent systems.

**Formal framework for ethical persuasion (EXTRAAMAS, Bhattacharya et al.).** Three core components: (1) a **user vulnerability model** quantifying susceptibility to manipulation; (2) a **clarity-transparency trade-off mechanism** adapting explanations to cognitive and contextual factors; (3) **time-decayed ethical bounds** preventing long-term manipulation. Validated on a health-coaching case study, adjusting persuasion strength by user profile and ethical constraints while preserving utility.

**Intention-aware policy graphs for autonomous vehicles (EXTRAAMAS, Montese et al.).** Explains AV behavior by surfacing the intentions behind policy decisions - interpreting intent is argued to be key for trustworthiness in safety-critical autonomy (AI4CCAM project context).

**Explainable MILP / X-MILP (EXTRAAMAS, Lera-Leri et al.).** Extends explainability beyond ML to optimization: uses constraint reasoning (irreducible infeasible subsystems) to build graphical explanations for mixed-integer linear programming, answering why a solution is infeasible or chosen.

**Surrogate decision trees with feature-importance matrices (EXTRAAMAS, Lavender & Sen).** Uses decision trees as interpretable surrogates approximating policies learned by black-box deep RL, recovering the obscured underlying strategy.

**Decolonized AI ethics and governance roadmap (EXTRAAMAS, Pansoni et al.).** Argues for governance frameworks for human-GenAI cooperation (e.g., in the art sector) that move beyond procedural definitions and Western-centric assumptions, addressing power, attribution, and cultural context.

**Blockchain-anchored accountable autonomy (Radanliev).** A reference pattern: record every agent decision (including RL/Q-learning policy choices) on a tamper-proof distributed ledger so autonomous decisions in finance, healthcare, and national security remain transparent, verifiable, and auditable. Combines (a) immutable ledger for audit, (b) smart contracts for automated enforcement, (c) smart oracles for trusted external data, (d) post-quantum cryptography for resilience.

**Platform selection for trust (Radanliev).** Ethereum (public, Solidity smart contracts, broad ecosystem; PoW -> PoS in Ethereum 2.0 for energy efficiency) vs. Hyperledger Fabric (permissioned, known identities, pluggable consensus, chain-code contracts - suited to privacy/compliance-heavy enterprise) vs. Corda (financial transactional privacy) vs. Cardano (research-driven, Ouroboros PoS, formal verification of smart contracts). The blockchain trilemma - scalability, security, decentralization - shapes every choice.

## Patterns

**Inter-agent explanation exchange** - Intent: let agents justify decisions to one another to improve cooperation and coordination. When to use: multi-agent systems where agents must trust or build on each other's outputs (BDI societies, heterogeneous agent teams). Use a unifying intention/belief/causality substrate so explanations are comparable across architectures. Trade-offs: adds communication and representation overhead; explanation formats must be standardized.

**Architecture-agnostic explainability substrate** - Intent: explain and compare decisions across BDI, RL, and LLM agents uniformly. When to use: heterogeneous agent ecosystems or when you need to audit different agent types together. Model decisions in terms of causality, beliefs, and intentions (Ladder of Intentions). Trade-offs: the abstraction may lose architecture-specific nuance.

**Objective-explanation evaluation** - Intent: verify explanations actually help, not just satisfy. When to use: any deployment relying on explanations for safety or decision quality. Measure objective mental-model improvement, not just user satisfaction (which cannot distinguish placebic from actionable explanations). Trade-offs: objective measurement is costlier than surveys.

**Ethically bounded persuasion** - Intent: prevent explanation systems from manipulating vulnerable users. When to use: persuasive or anthropomorphic agents (health coaching, advisory systems). Model user vulnerability, adapt clarity/transparency to context, and impose time-decayed bounds on persuasion. Trade-offs: bounding persuasion may reduce short-term engagement or compliance.

**Immutable decision audit trail** - Intent: make autonomous agent decisions tamper-evident and accountable. When to use: autonomous agents in regulated or high-stakes domains (finance, healthcare, national security). Record decisions (including RL policy outputs) on a distributed immutable ledger. Trade-offs: storage cost, throughput limits, and the difficulty of reconciling immutable records with dynamic AI data and privacy/erasure requirements.

**Smart-oracle grounding** - Intent: let on-chain agent logic act on trusted real-world data. When to use: agents whose decisions depend on external events (markets, weather, supply chains). Use AI-augmented or decentralized oracle networks for accuracy and anti-manipulation. Trade-offs: oracles are a trust and attack surface; decentralization adds latency and cost.

**Permissioned vs. public ledger selection** - Intent: match the trust substrate to confidentiality and compliance needs. When to use: deciding where to anchor agent accountability. Permissioned (Hyperledger Fabric, Corda) for known-identity, privacy/compliance-heavy enterprise; public (Ethereum) for open, maximally transparent ecosystems. Trade-offs: the trilemma - you cannot maximize scalability, security, and decentralization simultaneously.

## Principles & best practices

- **Explainability is a safety and trust requirement, not a nicety**, especially in healthcare, criminal justice, and autonomous driving where black-box decisions are unacceptable (EXTRAAMAS; Radanliev).
- **Evaluate explanations objectively.** User satisfaction cannot tell meaningful explanations from placebic ones (EXTRAAMAS, Shymanski et al.).
- **Design against manipulation.** Persuasive and anthropomorphic features can exploit cognitive vulnerability; bound persuasion ethically and over time (EXTRAAMAS, Bhattacharya et al.).
- **Interpreting intent is key for trustworthiness** in safety-critical autonomy (EXTRAAMAS, Montese et al.).
- **Extend explainability beyond ML** to optimization and agent decision processes (EXTRAAMAS, Lera-Leri et al.; Gimenez-Abalos et al.).
- **Govern responsibly and inclusively.** Move beyond procedural and Western-centric ethics frameworks (EXTRAAMAS, Pansoni et al.).
- **Make autonomous decisions auditable.** Anchor agent decisions to immutable ledgers so accountability survives autonomy (Radanliev).
- **Cryptography alone is insufficient; assume breaches and audit.** Pair post-quantum cryptography with tamper-evident logging (Radanliev).
- **Match the trust substrate to the domain.** Permissioned ledgers for compliance/privacy; public ledgers for open transparency (Radanliev).
- **Align EU and international standards.** Privacy regulation (EU AI Act, GDPR) is increasingly a design input, not just legal overhead (EXTRAAMAS; Radanliev).

## Anti-patterns & pitfalls

- **Treating user satisfaction as evidence of good explanation.** Placebic explanations satisfy without informing (EXTRAAMAS).
- **Anthropomorphic, persuasive agents with no manipulation safeguards.** Risk exploiting vulnerable users (EXTRAAMAS).
- **Architecture-specific, incomparable explanation techniques.** Proliferating ad hoc methods that cannot be validated or compared across agent types (EXTRAAMAS).
- **Human-only explainability in multi-agent systems.** Ignoring agent-to-agent explanation forgoes a key cooperation enabler (EXTRAAMAS).
- **Procedural-only or culturally narrow ethics frameworks.** Inadequate for global, cooperative human-GenAI settings (EXTRAAMAS).
- **Autonomous agents with no audit trail.** Unaccountable decisions in high-stakes domains (Radanliev).
- **Relying on classical cryptography against quantum adversaries.** Shor's/Grover's algorithms threaten it; needs post-quantum schemes (Radanliev).
- **Ignoring the blockchain trilemma.** Pretending you can maximize scalability, security, and decentralization at once (Radanliev).
- **Forcing immutable ledgers onto dynamic AI data without reconciling privacy/erasure.** Immutability conflicts with data-protection rights and AI's changing data needs (Radanliev).

## Decision guidance

When building trustworthy, responsible agents:

1. **Determine the trust audience.** Humans, other agents, regulators, or all three - this selects explanation targets and formats.
2. **Choose explanation depth by stakes.** Safety-critical or regulated -> intention-aware, objectively-evaluated explanations; low-stakes -> lighter explanations.
3. **Screen for manipulation risk.** If the agent persuades or is anthropomorphic, add vulnerability modeling and time-decayed ethical bounds.
4. **Decide whether decisions need tamper-evidence.** High-stakes/regulated autonomy -> anchor to an immutable ledger; record RL/policy decisions for audit.
5. **Pick the ledger by confidentiality needs.** Permissioned (Fabric/Corda) for privacy/compliance; public (Ethereum) for open transparency; weigh the trilemma.
6. **Ground external data through trusted oracles** where agents act on real-world events.
7. **Plan for quantum resilience** with NIST post-quantum cryptography, and assume breaches by keeping auditable logs.
8. **Align with governance and standards** (EU AI Act, international blockchain standards) as design inputs from the start.

## Source attribution

- XAI fundamentals, inter-agent explainability, Ladder of Intentions, BDI inter-agent explainability roadmap, actionable-vs-placebic explanations, ethical-persuasion formal framework, intention-aware AV policy graphs, X-MILP, surrogate decision trees, decolonized AI ethics roadmap, objective explanation evaluation: Calvaresi, Najjar, Omicini, Aydogan, Carli, Ciatto, Tiribelli, Framling (eds.), *Explainable, Trustworthy, and Responsible AI and Multi-Agent Systems* (EXTRAAMAS 2025, Springer LNAI 15936, 2026).
- Digital trust via blockchain, immutability, smart contracts and chain code, smart oracles and oracle networks, post-quantum cryptography, Ethereum/Hyperledger/Corda/Cardano platform trade-offs, blockchain trilemma, blockchain-anchored accountable autonomy, recording RL/Q-learning decisions on immutable ledgers: Radanliev, *The Rise of AI Agents: Integration of AI, Blockchain Technologies and Quantum Computing* (Addison-Wesley, 2025).

## Recent developments (2024-2026)

- **Inter-agent explainability is becoming its own research thread.** 2025-26 work moves explainability beyond human audiences toward agents that exchange explanations to coordinate, with unifying frameworks (Ladder of Intentions) spanning BDI, RL, and LLM agents (EXTRAAMAS 2025).
- **Explanation evaluation is maturing past satisfaction surveys.** Evidence that satisfaction cannot distinguish actionable from placebic explanations is pushing the field toward objective mental-model measures (EXTRAAMAS 2025).
- **Manipulation and ethical persuasion are formalized.** New frameworks model user vulnerability and impose time-decayed ethical bounds on persuasive XAI, driven by anthropomorphic agent risks (EXTRAAMAS 2025).
- **Governance is broadening.** Calls for decolonized, inclusive AI ethics frameworks for human-GenAI cooperation reflect the globalization of agent deployment (EXTRAAMAS 2025).
- **Blockchain-anchored accountability for autonomous agents** is being positioned as the structural answer to the trust gap as agents act autonomously in finance, healthcare, and national security, combining immutable audit trails, smart contracts, AI-augmented oracles, and post-quantum cryptography (Radanliev, 2025).
- **Post-quantum cryptography (NIST-compliant lattice/hash-based)** is moving from research to a recommended baseline for securing AI and blockchain systems against emerging quantum threats (Radanliev, 2025).
- **Regulatory alignment (EU AI Act, GDPR) is now a design input** for trustworthy agent systems rather than downstream legal compliance (EXTRAAMAS 2025; Radanliev, 2025).

Updates as of June 2026 sharpening the regulatory and standards backdrop:

- **EU AI Act obligations are now partly in force on a concrete timeline.** Prohibited practices and AI-literacy duties applied February 2025; GPAI model obligations and governance applied August 2025 (the GPAI Code of Practice published July 2025); full applicability, including most high-risk and transparency rules, is August 2026, with extended transitions to 2027-2028 for some categories. Trustworthiness and explainability requirements now carry real deadlines and penalties (up to EUR 35M or 7% of global turnover) (EU Commission, 2025-2026).
- **Agent-specific governance standards are forming.** NIST's Center for AI Standards and Innovation launched an AI Agent Standards Initiative (February 2026) covering agent identity/authorization, security, and monitoring/logging, with an Interoperability Profile planned for Q4 2026, complementing the explainability and audit-trail concerns in this file (NIST / Pillsbury, 2026).
- **OWASP published a dedicated Top 10 for Agentic AI (December 2025),** formalizing accountability and trust risks (excessive agency, tool misuse, memory poisoning) that complement this file's structural-trust and audit-trail patterns (OWASP GenAI Security Project, 2025).
- **Agent identity and accountability are converging on protocol-level mechanisms.** MCP (now a Linux Foundation standard, December 2025) adopted OAuth 2.1 authorization and A2A uses Agent Card identity, giving the "digital-identity-bound agent" and audit-trail patterns standardized, non-blockchain implementation paths alongside the ledger-based approaches Radanliev describes (Linux Foundation, 2025).

Sources: [EU AI Act timeline](https://artificialintelligenceact.eu/implementation-timeline/), [NIST AI Agent Standards (Pillsbury)](https://www.pillsburylaw.com/en/news-and-insights/nist-ai-agent-standards.html), [OWASP Top 10 for Agentic AI](https://genai.owasp.org/2025/12/09/owasp-genai-security-project-releases-top-10-risks-and-mitigations-for-agentic-ai-security/), [One Year of MCP](https://blog.modelcontextprotocol.io/posts/2025-11-25-first-mcp-anniversary/).
