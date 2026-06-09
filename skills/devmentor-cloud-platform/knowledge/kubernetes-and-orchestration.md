# Kubernetes and Orchestration

**Domain:** 09 - Cloud Infrastructure and Platform
**Primary sources:** Kubernetes – An Enterprise Guide, 3rd Edition (Packt, 2024)

## Overview

Kubernetes is the dominant container orchestrator: a declarative, API-driven control system that schedules containers across a fleet of machines, keeps them running and healthy, scales them, networks them, and self-heals when nodes or pods fail. Where Docker Compose manages a single host's stack, Kubernetes manages a cluster. Its central abstraction is reconciliation: you declare a desired state in a manifest (YAML or JSON), and controllers continuously drive the cluster's actual state toward that desired state. This declarative, control-loop model is what makes Kubernetes resilient and automatable, and it is why it became the default substrate for cloud-native platforms.

In the SDLC, Kubernetes is where built and shipped container images actually run in production. It matters because it standardizes deployment, scaling, networking, configuration, secrets, and storage across heterogeneous environments (on-premises, public cloud, edge), and because it is extensible: custom resources and operators let teams encode operational knowledge as software. Enterprise adoption adds concerns that go beyond a single cluster: multi-tenancy, centralized authentication and RBAC, secrets management, policy enforcement, and GitOps-driven delivery. This file focuses on Kubernetes architecture, core objects, scheduling and scaling, networking and storage, extensibility, and the enterprise operating concerns that surround it.

## Core concepts

**Declarative, API-driven model.** Kubernetes is entirely API-driven: every interaction, whether through `kubectl` or direct HTTP, goes through the API server. You interact imperatively (telling Kubernetes what to do, useful for quick fixes and testing) or declaratively (sending a manifest that declares the desired state, the norm in production). All API traffic is JSON even when manifests are authored in YAML.

**Control plane.** The control plane has authority over the whole cluster; without it the cluster cannot schedule workloads, create deployments, or manage objects. It is deployed for high availability with an odd number of nodes (a minimum of three). Its components:
- **kube-apiserver** - the front door. Every request goes through it; if it is down, all cluster requests fail. With multiple control-plane nodes it sits behind a load balancer (F5, HAProxy, Seesaw).
- **etcd** - a distributed key-value database that stores all cluster data. Every resource maps to a key under `/registry/<resource>`. It is the foundation of the cluster; you rarely touch it directly (kubectl queries the API server, which queries etcd). Some distributions replace it: k3s (and vCluster built on it) can use a relational database such as SQLite.
- **kube-scheduler** - assigns unscheduled pods to nodes by evaluating resource requirements, constraints, selectors, and affinity/anti-affinity rules, narrowing to feasible nodes and choosing the best.
- **kube-controller-manager** - runs many controllers in one binary, each a non-terminating control loop that watches a resource and drives current state to desired state (Endpoints, Namespace, Node, Replication, ServiceAccounts controllers, and more).
- **cloud-controller-manager** - integrates cloud-provider features (load balancers, persistent storage, autoscaling groups) when running on a managed cloud.

**Worker node components.**
- **kubelet** - the agent on every worker node ensuring its containers are running and healthy; reports node data the scheduler uses.
- **kube-proxy** - manages network connectivity and traffic routing to destination pods for services. Depending on the CNI it may be absent (Cilium can run kube-proxy-less; Calico relies on it).
- **container runtime** - runs the containers. Docker is one option, but containerd and CRI-O have largely replaced it as the preferred runtimes.

**Resources.** Adding, deleting, or scaling anything means interacting with Kubernetes resources, each of which expresses desired state the API server reconciles. A base cluster ships roughly 58 resources; add-ons (Calico, CSI drivers, operators) extend the API, so production clusters often exceed 100. Resources are either namespaced (scoped to a namespace, for example pods, deployments, services, configmaps, secrets) or cluster-level (for example nodes, namespaces, persistent volumes, storage classes, cluster roles).

**Pod.** The smallest deployable unit, wrapping one or more containers that share networking and storage. Pods are ephemeral and get an IP that changes on restart. You should almost never create a pod directly (except for quick troubleshooting) because bare pods cannot scale, self-restart, or roll. Use a higher-level controller instead.

**Workload controllers.**
- **Deployment** - the default for stateless apps. Creating one is a three-step chain: Deployment creates a ReplicaSet, which creates the pods. Deployments give declarative rolling upgrades, rollbacks to previous revisions, and scaling. Use them by default even when you do not yet need scaling or rolling.
- **ReplicaSet** - maintains a set number of pod replicas (creates/deletes pods to match the count). Rarely created directly; a Deployment manages it.
- **ReplicationController** - the older predecessor to ReplicaSet; prefer Deployments.
- **StatefulSet** - for stateful apps. Gives stable, known pod names (web-0, web-1, web-2), ordered deployment/scaling/updates (web-0 must be ready before web-1), and per-pod persistent storage via a VolumeClaimTemplate (PVCs named like www-web-0).
- **DaemonSet** - runs one pod on every node (or a selected set), and on new nodes as they join. Used for per-node agents like logging (Fluentd) or networking.
- **Job / CronJob** - a Job runs a pod to completion a set number of times; a CronJob runs Jobs on a schedule.

**Service.** A stable network endpoint in front of a changing set of pods. A pod's IP changes on restart and there may be many pods after scaling, so a Service receives its own durable IP and dynamically maintains a list of target pods (endpoints) by matching its selector to pod labels. Service types:
- **ClusterIP** (default) - reachable only inside the cluster (still externally exposable via Ingress).
- **NodePort** - exposes the service on a random port (30000–32767) on every node, routed by kube-proxy.
- **LoadBalancer** - provisions an external cloud load balancer with a public IP; on-premises needs an add-on such as MetalLB.
- **ExternalName** - maps an internal DNS name to an external service (no IP assigned).

**Endpoints and EndpointSlices.** Endpoints map a service to its backing pods. Endpoints do not scale well (all endpoints in one object); EndpointSlices shard them (100 endpoints per slice by default), so deleting a pod updates only one small slice, keeping control-plane and network traffic efficient at scale.

**Ingress.** A resource defining rules for incoming HTTP/HTTPS traffic, routing by hostname, path, or headers to services, and exposing applications to the outside world under assigned URLs. An Ingress controller (NGINX, Envoy, Traefik, HAProxy) implements the rules as a Layer 7 load balancer. IngressClasses let multiple Ingress controllers coexist in one cluster and bind specific Ingress objects to specific controllers.

**ConfigMaps and Secrets.** A ConfigMap stores non-sensitive configuration as key-value pairs (from literals, files, or directories), separating config from the application image. A Secret holds sensitive data; importantly it is only Base64-encoded, not encrypted, so the value is trivially decodable. The point of a separate Secret type is easier access control and the ability to inject from an external secret manager. Everything lands in etcd, so a stolen etcd database exposes all Base64 secrets; Kubernetes supports encryption-at-rest for secrets, and enterprises commonly externalize to HashiCorp Vault or CyberArk Conjur.

**Namespaces.** Logical partitions of a cluster enabling granular management of permissions, quotas, and reporting. They are the basic unit of soft multi-tenancy.

**Storage: PV, PVC, StorageClass, CSI.** A PersistentVolume (PV) represents real storage; a PersistentVolumeClaim (PVC) is a pod's request to consume storage and binds to a PV. Maintaining PVs by hand is messy, so the Container Storage Interface (CSI) lets Kubernetes manage common storage systems and auto-provision PVs from PVCs. A StorageClass names a storage tier with its own provisioner and policies (for example a `fast` NVMe class and a `standard` NFS class), letting developers pick the right storage per workload.

**Networking model and CNI.** Pods get IPs from a cluster CIDR range, usually addressable only inside the cluster ("island mode"). The Container Network Interface (CNI) is a specification letting Kubernetes plug in network implementations (Calico, Flannel, Cilium, Kindnet, and more). The cluster's internal DNS (CoreDNS) provides service discovery by name.

**RBAC and ServiceAccounts.** Kubernetes is secure by default: every access to a resource or service is evaluated against role-based access control (RBAC). Roles/ClusterRoles define permissions; RoleBindings/ClusterRoleBindings grant them to subjects. Workloads use ServiceAccounts as their identity, combined with bindings and roles to access services or objects.

**Admission control and webhooks.** MutatingWebhookConfigurations intercept and modify API requests (adding/removing/changing fields and annotations); validating webhooks accept or reject them. These are the extension points for policy enforcement (for example Open Policy Agent / Gatekeeper).

**CustomResourceDefinitions (CRDs) and operators.** A CRD lets users define their own resource types, which then behave as first-class, built-in resources managed with normal Kubernetes tooling. The operator pattern pairs a CRD with a custom controller that watches for instances and acts on them, encoding operational knowledge (for example provisioning databases or even cloud infrastructure via the AWS Controllers for Kubernetes). CRDs are cluster-level, which creates version-conflict challenges in multi-tenant clusters.

**Helm.** The de facto Kubernetes package manager. It packages applications as charts, simplifying defining, installing, and managing complex applications. Charts accept custom values on the CLI or via a values file passed with `-f`, making deployments reproducible and parameterized; many vendors and projects ship their software as Helm charts.

## Frameworks & models

**Reconciliation / control-loop model.** Controllers run non-terminating loops: observe current state, compare to desired state, act to close the gap. Scaling a deployment from 1 to 3 replicas means the replication controller notices the difference and creates two more pods. This single idea underpins deployments, services, autoscalers, and operators.

**Scheduling model.** The scheduler filters nodes to feasible candidates (capacity, taints/tolerations, node selectors, affinity/anti-affinity) then scores and picks the best. Supporting resources tune this: PriorityClasses prioritize pods for resource allocation and scheduling; PodDisruptionBudgets cap how many pods may be unavailable at once (protecting availability during upgrades, with care not to deadlock an upgrade); ResourceQuotas and LimitRanges bound consumption per namespace.

**Scaling spectrum.**
- **Horizontal Pod Autoscaler (HPA)** - automatically scales the number of pods based on metrics (CPU, memory, or custom), up to a max and back down to a min after a cooldown. The primary automated scaling mechanism for pods.
- **Vertical Pod Autoscaler (VPA)** - adjusts the CPU/memory requests of pods rather than their count (complementary to HPA, typically not on the same metric).
- **Cluster Autoscaler** - adds or removes nodes so pods that cannot be scheduled get capacity and idle nodes are reclaimed.

**Layer 7 vs Layer 4 load balancing (OSI model).** Layer 4 operates on TCP/UDP and can expose any port (needed for non-HTTP workloads like databases). Layer 7 operates on application protocols (HTTP/HTTPS) and routes by content; Kubernetes Ingress controllers are Layer 7 load balancers. Key rule: a database cannot be exposed via a Layer 7 load balancer; non-HTTP traffic requires Layer 4.

**Enterprise multi-tenancy model.** The early "cluster per application" pattern is inefficient: every cluster needs an odd number of etcd nodes, multiple control-plane and worker nodes, duplicated scarce Kubernetes skills, and per-cluster integrations for auth and secrets. Multi-tenancy pools infrastructure, centralizes security/policy/auth/secrets enforcement, and cuts cost, at the price of harder isolation, blast-radius management, resource bottlenecks, CRD-versioning conflicts, and the organizational silo problem (application owners resist centralization that reduces their control). Two axes to design: tenant boundaries (scope and control within them) and self-service (how tenants interact with the platform).

**Virtual clusters (vCluster).** A virtual cluster runs a full Kubernetes control plane (its own API server plus database, etcd or relational) inside an unprivileged namespace of a host cluster. A synchronization engine syncs objects (notably pod definitions, with DNS/env rewritten so the pod sees the vCluster as its API server) into the host namespace where pods actually run. Tenants get what looks like a private cluster with cluster-admin and can deploy their own namespaces, CRDs, and operators, while host-level ResourceQuotas, policies, admission controllers, and the shared Ingress controller still apply. This isolates tenants (CRD versions, control) without compromising host security, and reduces duplicated Ingress and policy deployments.

## Patterns

- **Declarative reconciliation (control loop)** - Intent: express desired state and let a controller continuously converge actual state toward it. When to use: everything in Kubernetes; the basis of self-healing and automation. Trade-offs: resilient and automatable, but behavior is eventually-consistent and asynchronous, which can be harder to reason about than imperative steps.

- **Deployment over bare Pod** - Intent: manage stateless workloads through a Deployment (→ ReplicaSet → Pods) to get rolling upgrades, rollbacks, self-healing, and scaling. When to use: any stateless app, by default. Trade-offs: one more layer of abstraction in exchange for all the lifecycle features; bare pods lose them.

- **StatefulSet for stable identity and storage** - Intent: run stateful apps needing stable network identity, ordered lifecycle, and per-pod persistent volumes. When to use: databases, clustered stores, anything order- or identity-sensitive. Trade-offs: stable names and per-pod PVCs at the cost of slower, ordered operations and more storage management.

- **DaemonSet for per-node agents** - Intent: guarantee exactly one pod per node for node-level concerns (logging, monitoring, networking). When to use: cluster-wide agents. Trade-offs: automatic node coverage including new nodes, but consumes resources on every node.

- **Service + label selector (stable endpoint)** - Intent: front a changing set of pods with a durable IP/name, auto-maintaining endpoints by label match. When to use: any workload other components must reach. Trade-offs: decouples callers from ephemeral pod IPs; requires consistent labeling.

- **Ingress (Layer 7 edge routing)** - Intent: route external HTTP/HTTPS to services by host/path/header through an Ingress controller. When to use: exposing web apps/APIs externally. Trade-offs: centralized TLS and routing vs needing an Ingress controller and being limited to HTTP/S (use Layer 4 / LoadBalancer service for other protocols).

- **HPA / VPA / Cluster Autoscaler (multi-level autoscaling)** - Intent: scale pods out/in (HPA), right-size pod resources (VPA), and grow/shrink the node pool (Cluster Autoscaler). When to use: variable load. Trade-offs: automated elasticity and cost control vs tuning thresholds, cooldowns, and avoiding HPA/VPA conflicts on the same metric.

- **External secrets management** - Intent: keep sensitive data out of etcd/Git by externalizing to Vault/Conjur and injecting at runtime, optionally with encryption-at-rest. When to use: compliance requirements and GitOps. Trade-offs: stronger security and auditability vs added integration and operational dependency.

- **Operator pattern (CRD + custom controller)** - Intent: encode operational knowledge as software by defining a CRD and a controller that reconciles instances. When to use: automating complex stateful systems or provisioning. Trade-offs: powerful, declarative automation of operations vs building and maintaining a controller, and cluster-wide CRD versioning constraints in multi-tenant clusters.

- **GitOps (Git as source of truth)** - Intent: store cluster and app configuration in Git and have controllers reconcile the live cluster to the repo. When to use: auditable, revertible delivery. Trade-offs: strong traceability and rollback vs the firm rule that secrets must never live in Git (externalize them).

- **Virtual cluster multi-tenancy** - Intent: give each tenant a nested virtual control plane in an unprivileged host namespace, with workloads still bound by host quotas/policies. When to use: enterprise multi-tenancy needing tenant-level cluster-admin, isolated CRD versions, and shared infrastructure. Trade-offs: strong tenant autonomy and host security with shared Ingress/policy, vs the operational complexity of running vClusters and a sync engine.

## Principles & best practices

- Run the control plane HA with an odd number (three or more) of control-plane and etcd nodes; front multiple API servers with a load balancer.
- Prefer declarative manifests in version control over imperative commands for anything production.
- Never deploy bare pods for real workloads; use Deployments (stateless) or StatefulSets (stateful).
- Always define resource requests/limits and apply ResourceQuotas and LimitRanges per namespace, especially in multi-tenant clusters.
- Use NetworkPolicies in all production clusters and treat them as mandatory in multi-tenant clusters to isolate namespaces.
- Keep client (`kubectl`) and cluster versions in sync (supported skew is within one minor version) to avoid feature mismatches.
- Treat Secrets as not-encrypted-by-default: enable encryption-at-rest and/or use an external secrets manager; never store secrets in Git.
- Externalize configuration with ConfigMaps and Secrets so one image runs everywhere.
- Use StorageClasses to offer tiered storage and CSI to auto-provision PVs rather than hand-maintaining volumes.
- Centralize authentication (OIDC), RBAC, secrets, and policy in multi-tenant clusters to avoid per-cluster duplication.
- Use PodDisruptionBudgets to protect availability during upgrades, but set them so they cannot deadlock an upgrade.
- Package and deploy applications as Helm charts (or equivalent) with values files for reproducible, parameterized installs.

## Anti-patterns & pitfalls

- Cluster-per-application sprawl: wasted hardware, duplicated scarce skills, and per-cluster auth/secrets integrations that are hard to automate.
- Creating bare pods, ReplicaSets, or ReplicationControllers directly instead of Deployments.
- Targeting pod IPs directly; they are ephemeral and change on restart. Use Services.
- Treating Secrets as secure because they look encoded; Base64 is trivially reversible and everything sits in etcd.
- Storing secret data in a Git repository (even encrypted): repos are easily forked and control is lost. A classic GitOps anti-pattern.
- Granting broad "everything except…" permissions so any tenant can, for example, have an operator provision real cloud resources with cost and security impact.
- Ignoring NetworkPolicies, leaving namespaces able to talk to each other freely.
- Trying to expose non-HTTP workloads (databases) through a Layer 7 Ingress instead of a Layer 4 / LoadBalancer service.
- Over-restrictive centralized platforms that push application owners to run shadow infrastructure, causing drift and audit gaps (the PaaS failure mode).
- Misusing EndpointSlices/Endpoints at scale by keeping everything in a single Endpoints object, overloading the control plane.

## Decision guidance

- **Deployment vs StatefulSet:** stateless and interchangeable pods → Deployment; stable identity, ordering, or per-pod storage → StatefulSet. DaemonSet for one-per-node agents; Job/CronJob for batch and scheduled work.
- **Service type:** internal-only → ClusterIP (default); simple external access on dev/on-prem → NodePort; cloud external with a real LB → LoadBalancer; alias to an external system → ExternalName. HTTP/S external routing → Ingress (Layer 7); non-HTTP external → Layer 4 LoadBalancer.
- **Scaling:** variable request load → HPA; right-sizing pod resources → VPA; node capacity pressure → Cluster Autoscaler. Combine but avoid HPA and VPA fighting over the same metric.
- **Secrets:** small or low-compliance clusters → native Secret objects (ideally with encryption-at-rest); compliance and GitOps → external manager (Vault/Conjur). Never Git.
- **Single cluster vs multi-tenant:** few teams, strong isolation needs, simple ops → dedicated clusters; many teams, cost pressure, centralized security → multi-tenant with namespaces + quotas + NetworkPolicies, escalating to vClusters when tenants need cluster-admin, custom CRDs/operators, or isolated CRD versions.
- **Extending the API:** static configuration → ConfigMaps/CRDs; operational automation of a stateful system or provisioning → operator (CRD + controller).
- **Packaging:** reproducible, parameterized app deployment → Helm charts with values files; raw manifests for the simplest cases.
- **CNI choice:** pick based on needed features (network policy enforcement, eBPF/kube-proxy-less with Cilium, mature policy with Calico); the CNI affects whether kube-proxy is present.

## Source attribution

- Cluster components, control plane (kube-apiserver, etcd, kube-scheduler, kube-controller-manager, cloud-controller-manager), worker node components (kubelet, kube-proxy, container runtime), API-server interaction and kubectl (imperative vs declarative, manifests, verbosity), and the full resource catalog with descriptions of pods, deployments, ReplicaSets, StatefulSets, DaemonSets, Jobs/CronJobs, Services and their types, Endpoints/EndpointSlices, Ingress/IngressClasses, ConfigMaps, Secrets, Namespaces, PV/PVC/StorageClass/CSI, NetworkPolicies, HPA, PriorityClasses, PodDisruptionBudgets, ResourceQuotas/LimitRanges, ServiceAccounts/RBAC, CRDs/operators, and admission webhooks: Kubernetes – An Enterprise Guide, 3rd Edition (Packt, 2024), Chapter 3 (Kubernetes Bootcamp).
- Services, Layer 7 vs Layer 4 load balancing, OSI model, and Ingress controllers (NGINX/Envoy/Traefik/HAProxy): same book, Chapter 4 (Services, Load Balancing, and Network Policies).
- CNI specification and CoreDNS/service discovery: same book, Chapters 2 and 6.
- Helm as package manager and chart values: same book, Chapters 2 and 5.
- Secrets managers, encryption-at-rest, Vault integration, and the GitOps "no secrets in Git" rule: same book, Chapter 8 (Managing Secrets).
- Enterprise multi-tenancy benefits/challenges, tenant boundaries, self-service, and vClusters: same book, Chapter 9 (Building Multitenant Clusters with vClusters).

## Recent developments (2024–2026)

- Kubernetes is now treated as the de facto "operating system" for AI: the CNCF 2025 Annual Cloud Native Survey (published Jan 2026) found 82% of container users run Kubernetes in production, and 66% of organizations hosting generative AI models use Kubernetes to manage some or all inference workloads. For the first time, the top barrier to cloud-native adoption is organizational/cultural rather than technical. (CNCF, 2026, https://www.cncf.io/announcements/2026/01/20/kubernetes-established-as-the-de-facto-operating-system-for-ai-as-production-use-hits-82-in-2025-cncf-annual-cloud-native-survey/)
- The sidecar era is ending. Istio ambient mesh (ztunnel for L4/mTLS per node plus optional waypoint proxies for L7) reached general availability in 2025, and Cilium's eBPF-native, kube-proxy-less networking has become a default for large deployments. Independent benchmarks show ambient's mTLS latency overhead far below classic sidecar Istio. (debugg.ai, 2025, https://debugg.ai/resources/sidecars-dying-ambient-mesh-ebpf-gateway-api-2025)
- The Kubernetes Gateway API has matured into the standard replacement for Ingress, unifying north-south and east-west routing across Envoy Gateway, Istio, Cilium, and Kong, and in 2026 added a Gateway API Inference Extension for AI model serving. (CNCF, 2026, https://www.cncf.io/announcements/2026/03/25/istio-brings-future-ready-service-mesh-to-the-ai-era-with-new-ambient-multicluster-gateway-api-inference-extension-and-more/)
- containerd 2.0 (released late 2024) is now the dominant CRI runtime, further cementing the post-dockershim runtime landscape (containerd/CRI-O over the legacy Docker shim). (DEV Community, 2025, https://dev.to/dataformathub/containerization-2025-why-containerd-20-and-ebpf-are-changing-everything-ph5)
- Platform engineering has become the dominant operating model around Kubernetes: Gartner-cited figures put platform-engineering adoption near 55% in 2025 with 80% expected by 2026, with internal developer platforms layering self-service and GitOps (used "extensively" by 58% of cloud-native innovators per CNCF) on top of multi-tenant clusters. (CNCF survey 2026; platformengineering.org, 2025, https://platformengineering.org/blog/top-10-platform-engineering-tools-to-use-in-2025)
