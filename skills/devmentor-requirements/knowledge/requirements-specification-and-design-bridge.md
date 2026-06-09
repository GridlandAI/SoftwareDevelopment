# From Specification to Design: The Bridge

**Domain:** 02 - Requirements & Specifications
**Primary sources:** Software Specification and Design: An Engineering Approach (Munson, 2006 - file labeled "JYanchak 2018"); More About Software Requirements: Thorny Issues and Practical Advice (Wiegers, 2009); Software Requirements Essentials (Wiegers & Hokanson, 2023)

## Overview
The boundary between "requirements" (what the system must do) and "design" (how it will do it) is famously fuzzy, and crossing it badly causes two opposite failures: requirements that secretly dictate design (over-constraining developers) and designs that drift from the requirements they are supposed to satisfy (building the wrong thing well). This file covers how to navigate that bridge: Wiegers' guidance on the fuzzy line, distinguishing genuine design constraints from incidental solution ideas; and Munson's engineering approach, which threads requirements through an **operational specification**, then a **functional specification**, then **design modules**, with explicit traceability mapping at every level. The goal is a controlled, auditable descent from stakeholder need to source code.

## Core concepts

### The fuzzy line between requirements and design
Wiegers' central reframe: the essential issue is **not "what versus how."** It is *distinguishing the real customer need from one possible description of how a clever developer might satisfy that need.* Requirements specifications routinely contain embedded solution ideas, and that is not automatically wrong - sometimes the embedded idea is a genuine **design constraint** that must be honored; sometimes it is merely a **suggestion** the customer offered to illustrate intent.

The diagnostic technique: when a stakeholder presents a requirement with an embedded solution, **ask "why" repeatedly** to drill from a stated "want" down to the underlying need.
- If the answer reveals a hard reason ("it must be done like this because of an interface/regulation/physical reality"), it is a true **design constraint** and belongs in the requirements.
- If the answer is "here's an example of what I have in mind," it is a solution idea that should be stripped out so designers retain freedom.

This is "an inquiry, not an inquisition" - the questioning is collaborative, aimed at understanding, not at challenging the stakeholder. (Wiegers, 2009, Part V: "The Fuzzy Line Between Requirements and Design" and "Solution Ideas and Design Constraints")

### Munson's engineering approach: layered specification
Munson treats specification as an engineering activity analogous to architecture (the Eiffel Tower was *engineered* - cheaper and lighter than stacking pyramid blocks). His method descends through clearly separated specification levels, each a more concrete machine than the last:

1. **Requirements** - stakeholder needs (the source).
2. **Operational specification** - defines the **abstract machine** the user interacts with: the system's **operations**, each triggered by a **stimulus**, with constraints (reliability, security, etc.) on the operational model. This is the user's view - what the system *does* from outside. A graphical representation models it.
3. **Functional specification** - defines the **functional model** (the virtual/system machine that implements the operations): the **functionalities** that realize each operation, with their own constraints. Configuration-controlled as a set of documents (Functional Overview, etc.).
4. **Design / modules** - **design modules** with a Module Header (control and data **coupling**), a Data Section (exact data consumed/produced), and an Algorithm Section (how the module transforms data). Quantifiable attributes of modules (coupling, flowgraph structure) make the design measurable.

Each level is a distinct, more-detailed and more-formal artifact, mirroring Wiegers' abstraction ladder and the REFSQ "requirements pyramid."

### Specification traceability (the spine of the bridge)
Munson makes traceability the load-bearing structure connecting the levels:
- **Requirements attribution** - every operation in the operational specification must implement a specific stakeholder requirement; implement *no* feature that is not traceable to a requirement.
- **Operation-to-functionality mapping** - each operation maps to the functionalities that realize it.
- **Functionality-to-module mapping** - each functionality maps to the design modules that implement it.

These mappings are the chain: requirement → operation → functionality → module → source code. The direct benefits (Munson devotes a chapter to them) include **reliability** and **maintainability** - maintainability attributes are explicitly defined in terms of the mappings (operation-to-functionality, functionality-to-module) and module flowgraph metrics. A key warning: *it frequently happens that the operational specification does not map well into the set of functional specifications* - a detectable, fixable disconnect, exactly like Wiegers' model-cross-checking.

### Why specification quality dominates lifecycle cost
Munson notes software **maintenance** now dominates lifecycle cost - often more than 80% - and "much of this maintenance cost is simply wasted money" traceable to poor specification. Modern systems (e.g., a fighter aircraft as "a flying software system") are mostly software whose specification quality determines total cost. This reinforces Wiegers' "push quality to the front."

## Frameworks & models

### Spotting embedded design in requirements (Wiegers)
A practical checklist for the fuzzy line:
- Scan requirements for **implementation-specific language** (UI widgets, specific technologies, screen layouts, algorithms).
- For each, ask **why** until you reach the underlying need.
- Classify the result: **genuine constraint** (keep, label as a constraint) vs. **incidental solution idea** (remove or demote to a non-binding note/example).
- Confirm constraints have a real source (a connected system's interface, a regulation, a physical limit, a corporate standard). Constraints without a justification are suspect.

### Munson's specification-to-design pipeline
| Level | Artifact | View | Key elements | Maps to |
|---|---|---|---|---|
| Requirements | Stakeholder needs | Customer | Needs, business rules | Operations |
| Operational spec | Abstract machine | What the system does (user-facing) | Operations, stimuli, model constraints | Functionalities |
| Functional spec | Functional model | How operations are realized (system machine) | Functionalities, constraints, Functional Overview | Modules |
| Design | Modules | Internal structure | Module headers (coupling), data sections, algorithms | Source code |

Verification/validation flows along the same mappings, enabling impact analysis: change a requirement and the traces show which operations, functionalities, and modules are affected.

### Convergence with the practitioner view
Both authors agree the descent is **iterative, not a clean waterfall.** Prototypes deliberately straddle the requirements/design boundary (Wiegers & Hokanson, Practice #12) - they make abstract requirements tangible while previewing design, and should be treated as experiments testing a hypothesis. The "right level of detail" before design is a risk decision: just enough that the team can build and verify at low risk, no more (Wiegers, "How Much Detail Do You Need?").

## Patterns

- **Ask "why" to separate constraint from suggestion** - *intent:* keep genuine constraints, strip incidental design. *When:* any requirement containing implementation language. *Trade-offs:* takes elicitation time; preserves designer freedom and prevents over-constraint.
- **Layered specification (operational → functional → module)** - *intent:* a controlled, auditable descent from need to code. *When:* engineering-grade systems where traceability and maintainability matter. *Trade-offs:* more artifacts and discipline; yields measurable design and cheap impact analysis.
- **Requirements attribution** - *intent:* ensure no feature exists without a requirement, and no requirement is unimplemented. *When:* throughout the pipeline. *Trade-offs:* tracing effort; eliminates gold-plating and gaps.
- **Cross-level mapping checks** - *intent:* detect where operational spec does not map cleanly to functional spec (or functional to modules). *When:* at each level transition. *Trade-offs:* review effort; finds disconnects before they reach code.
- **Prototype as boundary-spanner** - *intent:* make requirements tangible and preview design to resolve uncertainty. *When:* IKIWISI needs, novel UI, feasibility doubts. *Trade-offs:* throwaway vs. evolutionary decision must be explicit; executable prototypes tempt premature release.

## Principles & best practices
- The requirements/design boundary is real-customer-need vs. one-possible-how; manage it by distinguishing constraints from suggestions, not by banning all "how."
- Keep genuine, justified design constraints in the requirements; remove incidental solution ideas so designers can optimize.
- Implement nothing that does not trace to a requirement (requirements attribution); investigate any requirement with no downstream operation/functionality/module.
- Maintain mappings at every level (requirement → operation → functionality → module → code) for impact analysis, maintainability, and reliability.
- Treat poor specification as the root of wasted maintenance cost - invest in specification quality up front.
- Descend iteratively; stop adding detail when the team can build and verify at acceptably low risk.
- Use prototypes as experiments at the boundary; decide throwaway vs. evolutionary intent before building, and build evolutionary prototypes with production-quality code.

## Anti-patterns & pitfalls
- **Embedding design in requirements unconsciously** - over-constraining developers with incidental UI/technology/algorithm choices presented as requirements.
- **Stripping out genuine constraints** because of a dogmatic "no how in requirements" rule - losing real interface/regulatory/physical limits.
- **Operational spec that does not map to the functional spec** - a silent disconnect that surfaces as defects in code if not caught at the level transition.
- **Building features with no requirement trace** (gold-plating) or requirements with no implementation (gaps).
- **Treating specification-to-design as a one-pass waterfall** - reality is iterative refinement.
- **Letting an executable prototype become the product** without production quality - incurs heavy technical debt.
- **Under- or over-detailing** the specification before design - both raise risk; calibrate to project risk.

## Decision guidance
- **Constraint or suggestion?** Ask why until you hit the underlying need. Hard external reason (interface, regulation, physics, corporate standard) → keep as a labeled design constraint. "Example of what I have in mind" → demote to a non-binding note and let designers choose.
- **How much specification before design?** Just enough that the team can build and verify the next chunk at low risk. More for outsourced/safety-critical/regulated work; less for co-located, low-risk, iterative work.
- **Which artifact am I writing?** If it states what the user-facing system does → operational spec / requirements. If it states how operations are realized internally → functional spec. If it states module structure, coupling, and algorithms → design.
- **When does a disconnect mean a problem?** Whenever an operation has no implementing functionality, a functionality has no module, or a model fails to map cleanly to the next level - that mismatch is a defect to resolve before proceeding.
- **Prototype throwaway or evolutionary?** Decide before building. Throwaway for pure requirements/UI clarification (build cheap, low fidelity, discard). Evolutionary only if you commit to production-quality code from the start.

## Source attribution
- The layered operational → functional → module specification model, abstract-machine framing, operations/stimuli, functionalities, modules with coupling/data/algorithm sections, requirements attribution, the operation-to-functionality and functionality-to-module mappings, specification traceability and its reliability/maintainability benefits, and the engineering-approach and maintenance-cost arguments are from **Software Specification and Design: An Engineering Approach** (Munson, 2006; file labeled JYanchak 2018).
- The fuzzy line between requirements and design, solution-ideas-vs-design-constraints, the "ask why" inquiry technique, managing scope creep, and "how much detail do you need" are from **More About Software Requirements** (Wiegers, 2009, Part V).
- Prototypes as boundary-spanning experiments and the iterative, risk-calibrated descent are reinforced by **Software Requirements Essentials** (Wiegers & Hokanson, 2023, Practice #12).

## Recent developments (2024–2026)
The requirements-to-design bridge (real need vs. one possible how, constraints vs. suggestions, layered specification, traceability as the spine) is conceptually stable. The 2024-2026 development reshapes the bridge into an explicit, AI-consumable pipeline and re-centers the long-running "what vs. how" tension.

- **Spec-driven development makes the bridge explicit and executable.** GitHub Spec Kit (Spec to Plan to Tasks to Implement) and Amazon Kiro (requirements.md in EARS, design.md, tasks.md) externalize the descent from requirements through design to code as maintained artifacts an AI agent traverses, closely paralleling Munson's requirement to operation to functionality to module layering and his requirements-attribution rule (implement nothing not traceable to a requirement). (GitHub Blog, 2025; Kiro, 2025; Martin Fowler, 2025, martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html)
- **"Intent as the source of truth."** A defining 2025 reframe holds that with AI, intent (the specification) becomes the source of truth and AI makes specifications executable, sharpening Wiegers' point that the boundary is about capturing the real need: an AI builder will faithfully implement embedded incidental design unless the spec separates genuine constraints from suggestions. (Martin Fowler, 2025; GitHub Blog, 2025)
- **Cross-level mapping checks gain an automation partner.** AI can now help detect where a requirement has no implementing design or task and where generated code diverges from the spec, mechanizing Munson's "operational spec does not map cleanly to functional spec" warning and Wiegers' model cross-checking. The discipline still requires humans to act on the mismatch. (industry tooling, 2025)
- **Prototypes-as-boundary-spanners are cheaper and faster.** AI app builders can produce working prototypes in hours, strengthening the prototype-as-experiment pattern at the requirements/design boundary, but raising the file's own caution: an AI-generated prototype is even more tempting to ship as production despite lacking production quality, the classic evolutionary-vs-throwaway trap. (Baytech Consulting, 2025; Kiro, 2025)
- **Specification quality dominates cost even more in the AI era.** Munson's argument that poor specification drives wasted maintenance cost is amplified when imprecise specs cause AI to generate large volumes of wrong or low-quality code (2025 studies report higher defect and vulnerability rates and code bloat), making the disciplined, traceable descent from need to code a direct lever on AI output quality. (Veracode, 2025; LeadDev, 2025)
