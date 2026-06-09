# Kubernetes and Container Security

**Domain:** 09 - Cloud Infrastructure and Platform
**Primary sources:** Learning Kubernetes Security, 2nd Edition (Raul Lapaz, 2025)

## Overview

A default Kubernetes configuration is not sufficient to protect deployed applications from attackers. Kubernetes is a large, fast-evolving ecosystem (kube-apiserver, etcd, scheduler, controllers, kubelet, kube-proxy, and the workloads on top), and the interactions between those components, plus the new entities an application introduces, create threat actors and attack surfaces that must be deliberately defended. Securing Kubernetes is therefore a layered, defense-in-depth discipline that spans the whole software development life cycle: from threat modeling at design time, through hardening the cluster and pods, scanning and signing images in the pipeline (shift left), to runtime detection and monitoring in production.

This topic matters because containers provide only minimal isolation (cgroups, namespaces, AppArmor/seccomp), the host kernel is shared, and a single misconfiguration (a privileged container, an over-broad RBAC grant, an unscanned image, an exposed dashboard) can lead to container escape, lateral movement, privilege escalation, or full cluster compromise. The model used throughout is to identify what you must protect (assets), the controls that protect them, the actors who threaten them, the surfaces they attack, and the mitigations, then to enforce least privilege and layered controls so that if one barrier is breached the next still holds.

## Core concepts

**Defense in depth.** The organizing principle: apply multiple, overlapping layers of security controls (authentication, encryption, authorization, logging, intrusion detection, network controls, image scanning, runtime protection) so no single failure is catastrophic. The Kubernetes equivalents of traditional controls all exist and should all be used.

**The layers to secure (a cloud-native security stack).** Security must be applied at every layer: the cloud/infrastructure the cluster runs on, the cluster (control-plane components, API server, etcd), the container (image contents, runtime), and the code/workload (application, configuration, RBAC, network policy). Weakness at any layer undermines the others. This layered framing is the cloud-native analog of the widely cited "4Cs" model (Cloud, Cluster, Container, Code).

**Threat modeling.** The process of analyzing the system during design to identify risks proactively and address security requirements early, reducing severity from the start. A threat-modeling session defines: the asset (what must be protected), the security control (safeguard or countermeasure), the threat actor (the entity that carries out a threat: script kiddies, nation-states, hacktivists, insiders), the attack surface (the part of the system the actor interacts with, including the entry point), the threat (the potential malicious activity), and the mitigation (how to reduce likelihood and impact). Output is a risk matrix of likelihood and impact with mitigations.

**Threat-modeling methodologies.** STRIDE (Microsoft, 1999): Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Escalation of Privilege, answering "what can go wrong?" PASTA (Process for Attack Simulation and Threat Analysis): a risk-centric, attacker-centric approach for asset-centric mitigation. VAST (Visual, Agile, and Simple Threat): integrates threat modeling across application and infrastructure development with Agile/SDLC. MITRE ATT&CK provides matrices of real-world tactics and techniques (including a Kubernetes matrix with tactics such as initial access, execution, persistence) that defenders map to controls.

**Threat actors in Kubernetes.** Three are distinguished by their position: end users (interact with Ingress, exposed Services, or open node ports; held off by nodes, pods, kube-apiserver, and the external firewall), internal attackers (already have pod/container access; held back by namespaces, access control via kube-apiserver, NetworkPolicy, and RBAC against lateral movement and escalation), and privileged attackers (kube-apiserver is the only boundary protecting master components, and if they compromise it, "it's game over").

**Security boundaries.** Each Kubernetes entity has built-in isolation derived from its design: containers (minimal isolation via cgroups, Linux namespaces, AppArmor and seccomp profiles), pods (add network and IPC isolation; SecurityContext and NetworkPolicies operate at pod level), nodes (kernel/hypervisor controls, AppArmor/SELinux, host hardening; pods can be pinned with nodeSelectors), clusters (a strong boundary isolating pods/containers from other clusters at network and system layers), namespaces (virtual clusters isolating pods/services; LimitRanger and NetworkPolicies apply here), and the API server (mediates all component communication so admins do not touch components directly). Security boundaries differ from trust boundaries; understanding both is essential.

**System-layer boundaries (Linux primitives).** Containers are processes with dedicated Linux namespaces. By default each pod has its own network and IPC namespace and each container its own PID namespace, giving good isolation. This is weakened when host namespaces are enabled in the pod spec (HostNetwork, HostPID, HostIPC) or when extra Linux capabilities are added (for example CAP_SYS_ADMIN or CAP_SYS_MODULE), which can enable container escape.

**Authentication.** kube-apiserver must identify the requester. Mechanisms include X509 client certificates, bearer/service-account tokens, authentication webhooks, an authentication proxy (trusting a verified identity in the X-Remote-User header), and user impersonation (Impersonate-User/Group headers, or `kubectl --as`, for debugging access policies). OpenID Connect (OIDC) with a centralized identity provider (Google, Okta, Keycloak, via bridges like Dex supporting LDAP/SAML/GitHub) is the recommended approach for strict production security because it supports MFA, SSO, granular access, and centralized auth-event logging.

**Authorization.** Determines whether an authenticated request is allowed or denied by evaluating request attributes (user, group, extra, API, request path, verb, resource, subresource, namespace, API group) against active modules in sequence. Modes: AlwaysAllow / AlwaysDeny (testing only), Node (grants kubelets scoped access; paired with the NodeRestriction admission controller so a kubelet can only modify objects on its own node), ABAC (attribute-based, JSON policy file), and RBAC. RBAC is the recommended mode: Roles/ClusterRoles define permissions and RoleBindings/ClusterRoleBindings grant them to users, groups, or service accounts. It is easy to update and scales well.

**Admission control.** Admission controllers intercept requests after authentication and authorization, before the object state changes. Mutating controllers modify requests (add labels, annotations, defaults, change settings); validating controllers enforce policy and reject violations (for example "no privileged containers" or "all pods must carry a label"). Admission runs in two phases (all mutating plugins, then all validating plugins); the order in the flag does not change phase order, but within a phase ordering can matter. Defaults (from v1.30) include NamespaceLifecycle, LimitRanger, ServiceAccount, PodSecurity, ResourceQuota, MutatingAdmissionWebhook, and ValidatingAdmissionWebhook. Webhook-based admission and ValidatingAdmissionPolicy (CEL) are the extension points for custom policy engines like OPA/Gatekeeper.

**Pod security.** PodSecurityPolicy was deprecated in v1.21 and removed in v1.25. It is replaced by Pod Security Standards (PSS) enforced by the built-in Pod Security admission controller (validating only, not mutating). PSS defines three profiles: Privileged (unrestricted, not for production), Baseline (blocks basic privilege escalation, a recommended starting point), and Restricted (highly restricted best practices, may break some workloads). Pod hardening is configured in the SecurityContext: run as non-root (runAsNonRoot), drop unneeded capabilities, allowPrivilegeEscalation: false, readOnlyRootFilesystem, and seccomp/AppArmor profiles.

**Secrets.** Kubernetes Secret objects are Base64-encoded, not encrypted, and stored in etcd (where data is not encrypted at rest by default). Enable encryption-at-rest and, for compliance and GitOps, externalize to a secrets manager such as HashiCorp Vault. Never store secrets in Git.

**Network policy.** NetworkPolicy resources define which pods may communicate (ingress and egress) with which endpoints. By default a pod can talk to all pods, so policies are needed to restrict pod-to-pod traffic and minimize lateral movement. They use selectors and ipBlock CIDRs (with `except`) and are a least-privilege control at the namespace level. They should be in every production cluster and are a must-have in multi-tenant clusters.

**Image security and the software supply chain.** Container images bundle the application binary, dependencies, configuration, and a base image, any of which can carry vulnerabilities. Because images are layered (copy-on-write), each Dockerfile instruction adds a layer that can introduce vulnerable packages. The supply chain needs an automated process to scan and patch images.

**Shift left.** Move security earlier in the pipeline: scan images for vulnerabilities (Trivy, Clair, Grype), generate a Software Bill of Materials (SBOM) at build time (Syft) to know exactly which library versions are present so you can react to new CVEs, integrate scanning into CI/CD, and sign and validate images to guarantee authenticity and integrity.

**Runtime security and observability.** At runtime, detect anomalous behavior and enforce policy. Falco (CNCF) detects runtime threats by matching system-call events and Kubernetes audit events against rules (engine + rules + the audit webhook backend). Tetragon (Cilium/Isovalent, eBPF-based) gives deep kernel- and application-level visibility (process executions, file access, network activity) and can block or log unwanted behavior at runtime. Auditing (the Kubernetes audit policy) records who did what to which object.

**Container escape.** The headline runtime threat: breaking out of a container to the host. Vectors include misconfigured/added Linux capabilities (CAP_SYS_ADMIN, CAP_SYS_MODULE), mounting sensitive host paths (for example /var/run/docker.sock), container-runtime vulnerabilities (CVEs in runc/containerd), abusing shared host namespaces (PID, network), vulnerable image contents, and privileged containers with access to host devices and the kernel.

## Frameworks & models

**Layered cloud-native security (4Cs-style).** Secure cloud, cluster, container, and code as concentric layers; each inner layer depends on the security of the outer ones. Use the framing to make sure no layer is left undefended.

**Threat-modeling workflow.** Define scope and understand component interactions, enumerate assets, apply a methodology (STRIDE / PASTA / VAST) and map to MITRE ATT&CK, identify threat actors and attack surfaces, then assign mitigations and security controls. Tools support this: kube-bench for CIS compliance, Trivy for scanning, kubectl impersonation and kube-monkey to simulate attacks and chaos and verify control effectiveness.

**Authentication then authorization then admission.** Every API request flows through three gates in order: who are you (authentication), are you allowed (authorization, RBAC recommended), and does the request satisfy policy and get mutated/validated (admission). Designing security means configuring all three deliberately.

**Pod Security Standards (Privileged / Baseline / Restricted).** A graduated policy model: start workloads at Baseline, move toward Restricted for production best practice, and reserve Privileged for the rare unrestricted case. Enforce per-namespace via the Pod Security admission controller, complementing SecurityContext settings.

**High availability as a security property.** Availability is part of security. Achieve HA at three levels: workloads (multiple replicas in Deployments/StatefulSets spread across nodes, or DaemonSets for per-node coverage), Kubernetes components (HA kube-apiserver, etcd, scheduler, controller-manager, since losing the API server means losing the cluster), and cloud infrastructure.

## Patterns

- **Threat modeling (proactive risk identification)** - Intent: analyze the system at design time to find and mitigate risks before deployment. When to use: every new application and cluster design, revisited as the system evolves. Trade-offs: surfaces risks early and cheaply but requires understanding component interactions and disciplined scoping (it can run forever if unbounded).

- **Principle of least privilege** - Intent: grant every subject (user, group, service account) and workload the minimum permissions and capabilities needed. When to use: RBAC roles/bindings, capabilities, network policies, and host access everywhere. Trade-offs: drastically limits blast radius of a compromise at the cost of careful role/scope design (namespace vs cluster-level, user vs service account, which resources/verbs). Some microservices need no API access at all.

- **RBAC over ABAC/AlwaysAllow** - Intent: use role-based access control as the authorization mode for scalable, auditable, least-privilege access. When to use: all production clusters. Trade-offs: easy to update and reason about vs needing deliberate role design; prefer namespaced Roles over ClusterRoles unless a permission genuinely must span namespaces.

- **Admission-control policy enforcement** - Intent: intercept API requests to validate (reject violations) and mutate (inject defaults) before objects are created. When to use: enforce organization-wide policy (no privileged pods, required labels, image provenance) via built-in controllers and validating/mutating webhooks (OPA/Gatekeeper, CEL ValidatingAdmissionPolicy). Trade-offs: centralized, cluster-wide enforcement vs added moving parts and ordering considerations.

- **Pod Security Standards + SecurityContext hardening** - Intent: constrain pods to Baseline/Restricted and harden via non-root, dropped capabilities, no privilege escalation, read-only root FS, and seccomp/AppArmor. When to use: all workloads. Trade-offs: strong reduction of escape/escalation surface vs occasional breakage of workloads that assume root or extra capabilities.

- **NetworkPolicy least-privilege segmentation** - Intent: default-deny and explicitly allow only required pod-to-pod and egress traffic to limit lateral movement. When to use: every production and especially multi-tenant cluster. Trade-offs: contains breaches and blast radius vs the effort of mapping required flows and maintaining policies (and needing a CNI that enforces them).

- **External secrets management** - Intent: keep sensitive data out of etcd-as-plaintext and out of Git by externalizing to Vault and injecting at runtime, with encryption-at-rest as a baseline. When to use: compliance requirements, GitOps. Trade-offs: stronger confidentiality and auditability vs integration and an operational dependency.

- **Shift-left image security (scan + SBOM + sign)** - Intent: scan images for CVEs, generate an SBOM to track dependencies, and sign/validate images, integrated into CI/CD so vulnerabilities are caught before deployment. When to use: every image build pipeline. Trade-offs: catches issues early and enables fast CVE response vs pipeline complexity and the need to manage signing keys and fail-the-build policy.

- **Runtime detection and response** - Intent: detect anomalous behavior at runtime (Falco on syscalls and audit events; Tetragon via eBPF) and alert/block, feeding a central logging system. When to use: production clusters needing detection of escapes, unexpected processes, and policy violations. Trade-offs: real-time visibility and enforcement vs tuning rules to avoid noise and the overhead of running agents.

- **Defense in depth + high availability** - Intent: layer multiple controls so a single breach is contained, and keep the security-relevant control plane and workloads highly available. When to use: all serious clusters. Trade-offs: resilience and containment vs cost and operational complexity (odd-numbered etcd/control-plane nodes, multiple replicas, load balancing).

## Principles & best practices

- Treat the default configuration as insufficient; deliberately add controls at every layer (cloud, cluster, container, code).
- Threat-model at design time and map controls to a methodology (STRIDE) and MITRE ATT&CK.
- Enforce least privilege everywhere: RBAC scoped to namespaces, minimal capabilities, default-deny network policies, no unnecessary host access.
- Use RBAC (and Node mode + NodeRestriction) for authorization; avoid AlwaysAllow in production.
- Prefer OIDC-based authentication with a central identity provider, MFA, and centralized auth logging for production.
- Run pods as non-root, drop capabilities, set allowPrivilegeEscalation: false, use read-only root filesystems, and apply seccomp/AppArmor; enforce Baseline/Restricted Pod Security Standards per namespace.
- Never run privileged containers in production; never mount the Docker/containerd socket or sensitive host paths into untrusted pods.
- Enable encryption-at-rest for Secrets and externalize sensitive data to a secrets manager; never put secrets in Git.
- Scan images for vulnerabilities, generate SBOMs, and sign and validate images, all integrated into CI/CD (shift left).
- Keep the host kernel, container runtime, and Kubernetes components patched.
- Deploy runtime detection (Falco/Tetragon) and enable audit logging, shipping events to a central system.
- Run the cluster highly available at workload, component, and infrastructure levels (odd-numbered etcd/control-plane nodes, multiple replicas).
- Avoid the default namespace for production workloads; isolate tenants and teams into namespaces with quotas and policies.

## Anti-patterns & pitfalls

- Relying on the default Kubernetes configuration and assuming it is secure.
- Running privileged containers, adding dangerous capabilities (CAP_SYS_ADMIN, CAP_SYS_MODULE), or enabling host namespaces (HostNetwork/HostPID/HostIPC) without need.
- Mounting the host Docker/containerd socket or sensitive host directories into containers, enabling trivial container escape.
- Treating Secret objects as encrypted: they are only Base64-encoded and sit in etcd unencrypted by default.
- Storing secrets in Git (an anti-pattern even when encrypted, because repos are forkable).
- Using AlwaysAllow authorization, or granting cluster-level RBAC where a namespaced Role would do, or granting broad roles to groups without understanding the blast radius.
- Leaving the Kubernetes Dashboard or API endpoints exposed without authentication (a common initial-access vector).
- Skipping image scanning and SBOMs, shipping images with known-vulnerable base layers and dependencies.
- Omitting NetworkPolicies, leaving default all-to-all pod communication that enables lateral movement.
- Running single instances of critical control-plane components, making availability (and thus security) fragile.
- Not patching the kernel, runtime, or Kubernetes, leaving known CVEs (for example runc/containerd escape CVEs) exploitable.

## Decision guidance

- **Authentication choice:** production with strict security → OIDC with a central IdP (MFA, SSO, audit logging), optionally via Dex; service-to-API → service-account tokens; existing reverse proxy → authentication proxy; debugging access → impersonation. Avoid long-lived static tokens.
- **Authorization mode:** production → RBAC (plus Node + NodeRestriction); never AlwaysAllow outside controlled tests; ABAC only where its file-based attribute model is specifically required.
- **Pod hardening level:** start workloads at Baseline, target Restricted for production; reserve Privileged for rare, justified cases. Enforce with Pod Security admission per namespace plus SecurityContext.
- **Policy enforcement:** simple invariants → built-in validating controllers and ValidatingAdmissionPolicy (CEL); complex org-wide policy → admission webhooks with OPA/Gatekeeper.
- **Secrets:** low-sensitivity, small clusters → native Secrets with encryption-at-rest; compliance/GitOps/multi-cluster → external manager (Vault). Never Git.
- **Image pipeline:** always scan (Trivy/Grype/Clair) and generate an SBOM (Syft); sign and validate images; decide whether to fail the build or warn based on severity and environment.
- **Runtime tooling:** need syscall/audit anomaly detection with a large rule community → Falco; need deep eBPF kernel visibility and inline enforcement → Tetragon. Both can coexist.
- **Network segmentation:** default-deny then allow-list; mandatory in multi-tenant clusters; ensure the CNI enforces NetworkPolicy.
- **Trade off isolation vs simplicity:** stronger isolation (Restricted PSS, strict network policies, no host access) reduces attack surface but can break workloads; tune per workload rather than globally weakening controls.

## Source attribution

- Kubernetes architecture and objects from a security lens (volumes, namespaces, service accounts, network policies, Pod Security admission), and Kubernetes alternatives: Learning Kubernetes Security, 2nd Edition (Lapaz, 2025), Chapter 1 (Kubernetes Architecture).
- Kubernetes networking model and Linux namespaces as isolation: same book, Chapter 2 (Kubernetes Networking).
- Threat modeling (assets, controls, actors, attack surface, threats, mitigations), STRIDE/PASTA/VAST, MITRE ATT&CK, component interactions (DaemonSet workflow), and threat actors: same book, Chapter 3 (Threat Modeling).
- Principle of least privilege, namespaces (default/kube-system/kube-public/kube-node-lease), RBAC role/binding scoping considerations, and NetworkPolicy egress/ingress least-privilege examples: same book, Chapter 4 (Applying the Principle of Least Privilege).
- Security boundaries (containers, pods, nodes, clusters, namespaces, API server), security boundaries vs trust boundaries, and system-layer boundaries (Linux namespaces and capabilities, host namespace settings): same book, Chapter 5 (Configuring Kubernetes Security Boundaries).
- Securing cluster components and component communication: same book, Chapter 6.
- Authentication mechanisms (X509, tokens, webhook, proxy/X-Remote-User, impersonation, OIDC/Dex), authorization (request attributes, AlwaysAllow/Deny, Node, ABAC, RBAC), and admission control (mutating/validating, two-phase, defaults, NodeRestriction): same book, Chapter 7 (Authentication, Authorization, and Admission Control).
- Pod Security Standards (Privileged/Baseline/Restricted), Pod Security admission, SecurityContext hardening, AppArmor/seccomp profiles: same book, Chapter 8 (Securing Pods) and the appendix.
- Shift left: container images and vulnerabilities, scanning with Trivy, SBOM with Syft, Grype, integrating scanning into CI/CD, and image signing/validation: same book, Chapter 9 (Shift Left: Scanning, SBOM, and CI/CD).
- Real-time monitoring, audit logging, and security log analysis: same book, Chapters 10 and 11.
- Defense in depth, high availability (workload/component/infrastructure), Vault secrets management, Falco anomaly detection, and Tetragon eBPF runtime protection: same book, Chapter 12 (Defense in Depth).
- Kubernetes vulnerabilities and container escapes (capabilities abuse, host-socket mounts, runtime CVEs, shared namespaces, privileged containers) and defensive controls: same book, Chapter 13.
- Third-party plugins for securing Kubernetes: same book, Chapter 14.

## Recent developments (2024–2026)

- Sigstore became the open standard for software supply-chain integrity around containers, with Cosign (sign/verify OCI artifacts), Fulcio (short-lived certificate CA), and Rekor (transparency log), paired with routine SBOM/IBOM generation to map components and detect drift. (youngju.dev, 2026, https://www.youngju.dev/blog/culture/2026-04-14-container-security-supply-chain-image-scanning-guide-2025.en; orthogonal.info, https://orthogonal.info/securing-kubernetes-supply-chains-with-sbom-sigstore-2/)
- eBPF moved from experimental to foundational for Kubernetes runtime security and observability: Falco and Tetragon provide kernel-level detection and (with Tetragon) inline enforcement of process, network, and file activity. (DEV Community, 2025, https://dev.to/dataformathub/containerization-2025-why-containerd-20-and-ebpf-are-changing-everything-ph5)
- Minimal, hardened base images became a primary defense: Docker made 1,000+ Hardened Images free and open source (Apache 2.0) in December 2025, reducing CVE exposure versus bloated base images, and the trend toward distroless/minimal images accelerated. (InfoQ, 2025, https://www.infoq.com/news/2025/12/docker-hardened-images/)
- Attack activity against the container supply chain rose sharply since early 2025 (compromised build systems, unverified registry pulls, misconfigured deployments), keeping image scanning, signing, and admission policy central to defense-in-depth. (youngju.dev, 2026, https://www.youngju.dev/blog/culture/2026-04-14-container-security-supply-chain-image-scanning-guide-2025.en)
- Security shifted "down" into the platform: rather than burdening developers ("shift left"), platform-engineering teams increasingly encode security/compliance controls into the IDP/orchestrator layer so they are enforced automatically. (DEV Community, 2026, https://dev.to/meena_nukala/platform-engineering-in-2026-the-numbers-behind-the-boom-and-why-its-transforming-devops-381l)
