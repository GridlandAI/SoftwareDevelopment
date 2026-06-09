# Containers and Docker

**Domain:** 09 - Cloud Infrastructure and Platform
**Primary sources:** Learning Docker, 2nd Edition (Raj, Chelladhurai, Singh, 2017); The Ultimate Docker Container Book, 4th Edition (Schenker, 2026)

## Overview

A container is a standardized, lightweight unit of software that packages an application together with all of its dependencies (runtime, libraries, configuration, and binaries) so that it runs the same way in any environment. The recurring analogy is the shipping container: once the industry agreed on standard dimensions, goods could be packed once and moved by any crane, truck, train, or ship without repackaging. Software containers play the same role in the software supply chain. Developers package an application once, and operators treat every container as an opaque, standardized black box that runs on any host carrying a container runtime. Docker coined the phrase "build, ship, and run anywhere."

Containers matter across the entire SDLC because they remove friction between development and operations, shorten release cycles from months to days or hours, and standardize the production estate so every server becomes "just another Docker host." Enterprises that containerize legacy applications and build a fully automated, container-based supply chain have reported maintenance cost reductions of 50 to 60 percent and release-cycle reductions of up to 90 percent. Containers also improve security by sandboxing applications with kernel primitives and by making immutable images easy to scan, sign, and verify. They are the substrate on which modern microservices, CI/CD, and cloud-native platforms (including Kubernetes) are built.

## Core concepts

**Container.** A container is not a lightweight virtual machine. It is one or more specially encapsulated and secured processes running directly on the host kernel. All containers on a host share that single Linux kernel. By contrast, each VM carries its own full operating system on top of a hypervisor. Container startup is measured in milliseconds versus seconds-to-minutes for VMs. Containers are meant to be ephemeral (they come and go quickly); VMs are meant to be long-lived.

**Containers vs. virtual machines.** VMs achieve isolation by emulating full machines under a hypervisor, giving complete OS-level isolation and a smaller attack surface (an attacker must subvert the guest kernel and the hypervisor before touching the host). Containers achieve isolation at the process level using namespaces and cgroups, sharing the host kernel. This makes them far denser (tens of containers per host versus a few VMs) and far more efficient, but it also means the host kernel is a shared single point of failure and the container attack surface is larger. The two are complementary: containers, VMs, and bare metal coexist in real estates.

**Namespaces.** Linux namespaces wrap a set of global resources (filesystem mounts, network stack, process tree/PID, user and group IDs, hostname, IPC) and present them to a process as if they were dedicated. Namespaces provide the first and most important form of isolation: a process in one container cannot see or affect processes in another. A process may appear as PID 1 inside its container but have an ordinary PID (for example 334) on the host. User namespaces (kernel 3.8) were the final piece that made containers practical.

**Control groups (cgroups).** cgroups limit, account for, and isolate the resource usage (CPU time, memory, network bandwidth, disk I/O) of groups of processes. They prevent the "noisy neighbor" problem where one rogue container starves all others, and they are a key defense against denial-of-service. cgroups v2 reached stable, broad adoption around 2022 and gives more precise multi-tenant accounting.

**Container images and the layered filesystem.** An image is the immutable template from which containers are created. Physically it is a tarball containing a layered filesystem. An image is a stack of read-only layers, starting with a base layer (often an official Linux distro such as Alpine, Ubuntu, or CentOS, or `scratch` for nothing at all). Each layer stores only the delta of changes relative to the layers beneath it. A Union filesystem (unionfs, via a storage/graph driver such as the recommended `overlay2`) merges these layers into a single coherent root filesystem.

**Immutability and copy-on-write.** Layers are immutable: once created a layer can never change, only be deleted. This enables caching (layers never go stale) and sharing (many containers reuse the same image layers). When a container starts, Docker adds one thin writable layer on top of the read-only stack. Copy-on-write means a layer that wants to modify a file from a lower layer first copies it up, then edits the copy, so containers never see each other's changes even on a shared image.

**Container runtime and plumbing.** A Docker host has three essential parts: the Linux OS (supplying namespaces, cgroups, and layer capabilities), the container runtime (`containerd` plus `runc`), and Docker Engine on top. `runc` is the low-level OCI runtime that spawns and runs containers with full support for namespaces, cgroups, SELinux, AppArmor, and seccomp. `containerd` builds on `runc` to add higher-level features (image transfer/storage, execution, supervision, networking) and manages the full container lifecycle. `containerd` is the CNCF reference implementation of the OCI spec and the default runtime for both Docker and Kubernetes 1.8+. Docker Engine adds networking libraries, plugin support, and a REST API consumed by the Docker CLI, Docker Desktop, and Kubernetes.

**The Open Container Initiative (OCI) / Open Container Project.** `runc` is a tool for spawning containers according to the OCI specification, a formally specified, vendor-neutral configuration format governed under the Linux Foundation. The OCI standard is what lets alternative runtimes (CRI-O from Red Hat, others) and tools interoperate. Docker donated `containerd`, `runc`, and `notary` to the CNCF.

**The Moby project.** When Docker (the company) split its open-source work from its commercial products, it created Moby as an umbrella framework of open-source components (image management, secret management, configuration, networking, provisioning) that can be assembled "like a Lego set" into custom container systems. Docker Engine itself is assembled from Moby components.

**Registries.** A registry stores and distributes images. Docker Hub is the most popular public registry. Docker Registry 2.0 (open source, Apache-licensed, written in Go, speaking the Registry HTTP API v2) is a stateless, horizontally scalable server with pluggable storage backends (local filesystem, S3, Azure, OpenStack Swift), native TLS and basic auth, and a webhook notification system that fires on manifest and layer push/pull events (useful for triggering CI/CD deployments). The enterprise variant historically was Docker Trusted Registry (DTR), adding high availability, RBAC, LDAP/AD integration, content-trust signing, and built-in security scanning.

## Frameworks & models

**Image namespaces and tagging.** Images are addressed as `registry/namespace/repository:tag`. Official images live in a well-known namespace and are curated and regularly scanned. Sharing an image is a three-step flow: log in to the registry, tag the image with the target username/namespace, then push.

**The Dockerfile.** A Dockerfile is the declarative manifest used to build an image reproducibly. Key instructions:
- `FROM` - choose the base image (or `scratch`).
- `RUN` - execute a command, creating a new layer.
- `COPY` / `ADD` - bring files into the image (`COPY` is preferred; `ADD` also fetches URLs and unpacks archives).
- `WORKDIR` - set the working directory.
- `ENV` / `ARG` - runtime environment variables and build-time arguments.
- `USER` - switch to a non-root user.
- `VOLUME` - declare a mount point for persistent data.
- `EXPOSE` - document the port the service listens on.
- `CMD` - default command/arguments (overridable at run time).
- `ENTRYPOINT` - the executable the container always runs (CMD supplies its default arguments).

**Build-time caching model.** Each Dockerfile instruction creates a layer. On rebuild, only changed layers (and every layer after them) are rebuilt. Ordering instructions from least-to-most frequently changed maximizes cache reuse (for example, copy `package.json` and run `npm install` before copying the rest of the source).

**Container networking model.** On install, Docker Engine creates three networks: `bridge` (default), `host`, and `none`. A new container by default joins the default `bridge` network: Docker creates a `docker0` Ethernet bridge in the kernel, reserves a private subnet (for example 172.17.0.0/16), and assigns the bridge the gateway address. Each container gets a network stack with a loopback (`lo`) interface and an `eth0` interface connected to `docker0` through a Virtual Ethernet (veth) pair (data sent on one end emerges from the other). `host` mode shares the host's network stack and ports; `none` gives only loopback (no external communication). Docker also supports `overlay` (multi-host), `macvlan`, and `ipvlan`. Container IPs are private and not reachable from outside the host; `docker inspect` (optionally with `--format`) reveals them.

**Data volumes and configuration.** The writable container layer is ephemeral and disappears with the container. For persistence Docker offers volumes (Docker-managed, stored under `/var/lib/docker/volumes`) and bind mounts (a host path mapped into the container). Volumes are preferred for portability, manageability, and security; bind mounts are useful for local development but expose host paths. Volumes can be shared between containers. Configuration is supplied through environment variables (per-variable or via an env file) and configuration files, keeping images environment-agnostic.

**Docker Compose.** Compose defines and runs multi-container applications from a single declarative YAML file (`docker-compose.yml`). The file groups top-level `services`, `networks`, and `volumes`. Each service specifies either an `image` or a `build` context plus keys that map to `docker run` options (`command`, `ports`, `depends_on`, `cap_add`, `deploy`, and so on). One command brings the whole stack up in the correct order with the right links, networks, and volumes. Compose is the standard way to create fast, isolated local environments and to orchestrate small multi-container stacks. `depends_on` expresses inter-service ordering. The Compose file format is versioned and tied to Engine capabilities (version 3 added `docker stack deploy` and `docker secrets`).

**Orchestration spectrum.** Compose handles a single host's stack. Beyond that, orchestration tools (Docker Swarm, Kubernetes) schedule and manage many containers across a fleet, handling service discovery, load balancing, rolling updates, and multi-host networking. Docker Swarm is Docker's built-in orchestrator integrated into the Engine; Kubernetes became the dominant orchestrator, which is why Docker sold its Enterprise Edition (UCP, DTR, enterprise engine) to Mirantis in 2019 and refocused on developer tooling (Docker Desktop, Docker Hub).

## Patterns

- **Multi-stage build** - Intent: produce a tiny, secure final image by splitting build and runtime into separate stages. Use one or more builder stages with all the SDKs/compilers, then a final minimal stage (`scratch`, Alpine, or distroless) that copies only the resulting artifacts via `COPY --from=<stage>`. When to use: any compiled or build-heavy application destined for production. Trade-offs: dramatically smaller images (the book's C example dropped from 260 MB to 136 KB), smaller attack surface, faster pulls and startup, at the cost of a slightly more involved Dockerfile.

- **Layer-cache ordering (immutable-layer leverage)** - Intent: minimize rebuild time by ordering Dockerfile instructions least-to-most volatile so expensive steps (dependency install) are cached across builds. Use when: iterating on source frequently. Trade-offs: faster builds and bandwidth savings; requires discipline in instruction order and separating dependency manifests from source.

- **Minimal-image hygiene** - Intent: keep images as small as possible via `.dockerignore`, avoiding unnecessary packages, and multi-stage builds. Use when: building any production image. Trade-offs: smaller attack surface, less disk/memory/bandwidth, faster startup; occasionally more effort to find a slim base.

- **Non-root container** - Intent: drop root privileges inside the container (via the `USER` instruction or `--user`) so a compromised application cannot trivially gain host-root. Use when: running any production container. Trade-offs: substantially reduced blast radius of a container breakout; some images need adjusted file permissions.

- **Immutable infrastructure** - Intent: never patch a running container/server in place; build a new immutable image and replace instances on every update. Use when: deploying containerized estates. Trade-offs: repeatability, trivial rollback, reduced management overhead, and tamper resistance, in exchange for requiring full automation and externalized state.

- **Container-as-a-service image** - Intent: package a long-running network service (web server, database) into an image with `ENTRYPOINT` launching the daemon in the foreground and `EXPOSE` documenting its port. Use when: shipping any server. Trade-offs: standardized, repeatable deployment; the container must run its main process in the foreground (PID 1).

- **Registry-driven CI/CD trigger** - Intent: use registry webhook notifications on image push to trigger staging deployments or notify downstream systems. Use when: building a container-based delivery pipeline. Trade-offs: clean event-driven delivery; couples the pipeline to registry events.

- **Named volume / data-volume container for stateful services** - Intent: persist data beyond the container lifecycle using Docker-managed named volumes (or a dedicated data-volume container) rather than the writable layer. Use when: any service that must keep state. Trade-offs: durable, portable, shareable storage; requires a backup/restore strategy and storage monitoring.

## Principles & best practices

- Treat containers as ephemeral: a container should be destroyable and replaceable with minimal setup and teardown time.
- Prefer official and verified images from trusted sources; never run random, untested images.
- Keep images small: multi-stage builds, `.dockerignore`, no unnecessary packages.
- Order Dockerfile instructions to maximize layer-cache reuse and combine related `RUN` commands to minimize layer count.
- Run with least privilege: non-root user, drop unneeded Linux capabilities, read-only filesystems where possible.
- Scan images for CVEs continuously (Trivy, Clair, Docker Scout, Snyk) and patch/update regularly.
- Sign and verify images (Docker Content Trust / Notary, or Cosign/Sigstore) to guarantee authenticity and integrity, defending against tampering, man-in-the-middle, and replay attacks. Generate an SBOM (Syft, CycloneDX) at build time so you can react to newly disclosed vulnerabilities.
- Prefer Docker-managed volumes over bind mounts; back up volumes and monitor storage usage; encrypt sensitive data at rest.
- Keep the host kernel patched and do not disable host OS security features (SELinux/AppArmor/seccomp).
- Externalize configuration through environment variables and config files so one image runs in every environment.
- Layer multiple security controls (defense in depth) so that if one containment barrier is breached, the next stops the attacker.

## Anti-patterns & pitfalls

- Treating a container as a lightweight VM and installing many services or an init system into one image.
- Running containers as root and as privileged, giving a breakout full host-root access.
- Bind-mounting sensitive host directories (for example mounting `/` into the container), letting the container alter the host filesystem.
- Loading arbitrary third-party images from tarballs (`docker load`) whose source the Engine cannot verify, risking poisoned images.
- Storing important state only in the writable container layer, so it is lost when the container is removed.
- Bloated images carrying full SDKs and build tools into production, increasing size, attack surface, and startup time.
- Invalidating the build cache by copying all source before installing dependencies, forcing slow rebuilds.
- Relying on container IP addresses as stable identifiers; they are private, host-local, and reassigned.
- Ignoring resource limits, allowing a noisy-neighbor or DoS container to exhaust host CPU/memory/IO.

## Decision guidance

- **Containers vs. VMs:** choose containers for density, fast startup, portability, and microservices; choose VMs (or VMs hosting containers) when you need strong kernel-level isolation between tenants or must run different OS kernels. They are complementary, not mutually exclusive.
- **Base image choice:** start from an official slim base (Alpine or distroless) for production; use `scratch` for statically compiled binaries. Larger bases (full Ubuntu) trade size for convenience and compatibility.
- **Single-stage vs. multi-stage:** any compiled or build-heavy app should use multi-stage to keep the runtime image minimal; interpreted apps with few build steps may not need it.
- **Volumes vs. bind mounts:** default to named volumes for production persistence and portability; use bind mounts mainly for local development where live host editing matters.
- **Compose vs. orchestrator:** use Compose for local development and small single-host stacks; move to Kubernetes (or Swarm) when you need multi-host scheduling, autoscaling, self-healing, and rolling updates across a fleet.
- **Registry choice:** public Docker Hub for open sharing; a private/self-hosted or enterprise registry (with RBAC, signing, scanning, and HA) when control, compliance, or air-gapped distribution is required.
- **Security posture:** combine non-root + dropped capabilities + read-only FS + seccomp/AppArmor/SELinux + image signing + continuous scanning. No single control is sufficient; defense in depth is the rule.

## Source attribution

- Container concept, the shipping-container analogy, business benefits, Moby project, Docker products, container architecture, "what's new" (image signing, SBOM, ephemeral containers, cgroups v2, rootless), the anatomy of containers (namespaces, cgroups, unionfs, runc/containerd, OCI), images and the layered/copy-on-write filesystem, graph drivers, Dockerfile instructions and best practices, multi-stage builds, save/load, image tagging/namespaces/official images, registry push, supply chain security practices, and volumes/configuration/persistent-storage best practices: The Ultimate Docker Container Book, 4th Edition (Schenker, 2026), chapters 1, 3, 4, and 5.
- Docker Registry 2.0 and DTR architecture, registry use cases and webhook-driven CI/CD, container networking model (bridge/host/none, docker0, veth, overlay/macvlan/ipvlan), container-as-a-service images, orchestration and Docker Compose (YAML structure, services/networks/volumes, version compatibility), and the in-depth container security chapter (VM vs. container attack surface, namespaces/cgroups isolation, root-privilege risks, non-root containers, SELinux/AppArmor/seccomp/SCONE, Docker Content Trust/TUF/Notary, Clair/Nautilus, image forgery/replay/key-compromise protection, secure-deployment guidelines, defense in depth): Learning Docker, 2nd Edition (Raj, Chelladhurai, Singh, 2017), chapters on registry, networking, orchestration, and securing Docker containers.

## Recent developments (2024–2026)

- Docker Hardened Images (DHI), launched as a commercial offering in May 2025, were made free and fully open source under Apache 2.0 in December 2025: 1,000+ minimal, low-CVE images built on Debian and Alpine, reflecting an industry shift from bloated base images to purpose-built minimal containers. (InfoQ, 2025, https://www.infoq.com/news/2025/12/docker-hardened-images/; Docker, https://www.docker.com/press-release/docker-makes-hardened-images-free-open-and-transparent-for-everyone/)
- containerd 2.0 (released late 2024) became the dominant Kubernetes CRI runtime, and Podman/rootless workflows continued gaining ground as Docker Engine alternatives in production. (DEV Community, 2025, https://dev.to/dataformathub/containerization-2025-why-containerd-20-and-ebpf-are-changing-everything-ph5)
- Supply-chain security hardened around containers: Sigstore (Cosign, Fulcio, Rekor) established itself as the open standard for image signing, and SBOMs became routine, with scanners like Trivy expanding from image scanning to continuous cluster monitoring, SBOM analysis, and VEX. (youngju.dev, 2026, https://www.youngju.dev/blog/culture/2026-04-14-container-security-supply-chain-image-scanning-guide-2025.en)
- eBPF-based runtime tooling (Falco, Tetragon) moved from experimental to foundational for container runtime detection and enforcement at the kernel level. (Root.io, 2025, https://www.root.io/blog/five-container-security-trends-at-kubecon-2025)
- Containers became core to agentic AI: Docker joined the Agentic AI Foundation and shipped Hardened MCP Servers, bringing image-hardening principles to the Model Context Protocol layer that agent tools rely on. (Docker, 2026, https://www.docker.com/newsletter-subscription/2026-01-12-free-dhi-security-ai/)
