# Docker & Kubernetes in a Nutshell

An interactive presentation on **Containers, Docker, Kubernetes, Observability, and Service Mesh**, powered by [Reveal.js 6.0.2](https://revealjs.com/).

* **Live Presentation:** [https://devops-cloud-courses.github.io/dockerkube](https://devops-cloud-courses.github.io/dockerkube)
* **Download Latest PDF:** [docker-kubernetes-in-a-nutshell.pdf](https://github.com/devops-cloud-courses/dockerkube/releases/latest/download/docker-kubernetes-in-a-nutshell.pdf)
* **PDF Export View:** [https://devops-cloud-courses.github.io/dockerkube/?print-pdf&pdfSeparateFragments=false](https://devops-cloud-courses.github.io/dockerkube/?print-pdf&pdfSeparateFragments=false)

---

## Course Curriculum

1. **Docker & Container Fundamentals** (`slides/docker.md`)
   * The shipping container analogy & eliminating the "Matrix from Hell"
   * Virtual Machines vs. Containers & why containers are lightweight
   * Linux kernel primitives: **`cgroups`** (resource metering & limits) and **`namespaces`** (`pid`, `net`, `mnt`, `ipc`, `uts`, `user`)
   * Docker Engine architecture (`docker` CLI, `dockerd`, `containerd`, `runc`) and **OCI** (`image-spec`, `runtime-spec`)
   * Union filesystems, Copy-on-Write (`CoW`) image layers, `Dockerfile` instructions, and multi-stage builds
   * Container lifecycle CLI (`docker build`, `docker run`, `docker ps`, `docker exec`, `docker logs`), volumes, and bridge networking
   * **Docker Compose** (multi-container local stacks) & **Podman** (daemonless, rootless, Pod-native architecture)
   * **Container Security** best practices (minimal/distroless base images, non-root `USER`, read-only rootfs, CVE scanning)
   * **CI/CD Pipelines** with containers & orchestrator comparison (Docker Swarm vs. Kubernetes)
2. **Kubernetes Architecture & Workloads** (`slides/kubernetes.md`)
   * Monolith vs. Microservices & container orchestration challenges at scale
   * Control Plane (`kube-apiserver`, `etcd`, `kube-scheduler`, `kube-controller-manager`) and Worker Nodes (`kubelet`, `kube-proxy`, CRI runtime)
   * **Namespaces** & **Pods** (atomic scheduling unit, shared network namespace & storage volumes, YAML manifests)
   * **Workload Controllers**: `Deployment`, `ReplicaSet`, `StatefulSet`, `DaemonSet`, `Job`, and `CronJob`
   * **Health Probes** (`startupProbe`, `readinessProbe`, `livenessProbe`) and zero-downtime **Rolling Updates**
   * **Networking**: `Service` (`ClusterIP`, `NodePort`, `LoadBalancer`), `Ingress`, and **Gateway API (`HTTPRoute`)**
   * **Configuration & Storage**: `ConfigMap`, `Secret`, `StorageClass`, `PersistentVolume` (`PV`), and `PersistentVolumeClaim` (`PVC`)
   * **Autoscaling & Packaging**: `HorizontalPodAutoscaler` (`HPA`) and **Helm v3** (Charts, `values.yaml`, client-only architecture)
   * **Monitoring**: Prometheus (`PromQL`), Alertmanager, Grafana, and OpenTelemetry (`OTel`)
3. **Logging in Kubernetes** (`slides/logging.md`)
   * Node-level logging (`stdout` / `stderr` capture and log rotation by `kubelet`)
   * Cluster-level logging architectures: DaemonSet node agents (**Fluent Bit**, **Vector**, **Grafana Alloy / OTel Collector**)
   * Sidecar streaming container & sidecar logging agent patterns
   * Direct application log push & architectural trade-offs
4. **Service Mesh** (`slides/service-mesh.md`)
   * Why a Service Mesh? Automatic mutual TLS (`mTLS`), traffic splitting / canary deployments, and distributed tracing
   * Data Plane vs. Control Plane architecture (**Istio**, **Linkerd**, **Cilium**)
   * **Sidecar** (per-Pod `Envoy` / `linkerd2-proxy`) vs. **Sidecarless** (Istio Ambient `ztunnel` node proxy & Cilium eBPF)

---

## Repository Structure

```text
.
├── index.html          # Reveal.js 6.0.2 entrypoint (jsDelivr CDN, zero-build setup)
├── css/
│   └── custom.css      # Custom light theme, high-contrast dark code blocks, and responsive layout
├── img/                # Custom vector SVG architecture diagrams & author avatars
└── slides/             # Modular Markdown slide decks loaded dynamically by Reveal.js
```

---

## Running Locally

Because Reveal.js loads external Markdown files (`slides/*.md`) via `fetch`, serve the repository over a local HTTP server rather than opening `index.html` via `file://`:

### Option 1: Python (built-in)
```bash
python3 -m http.server 8000
```
Then open [http://localhost:8000](http://localhost:8000).

### Option 2: Node.js (`npx`)
```bash
npx serve .
```

### Option 3: Docker (`nginx`)
```bash
docker run --rm -p 8080:80 -v "$PWD":/usr/share/nginx/html:ro nginx:alpine
```
Then open [http://localhost:8080](http://localhost:8080).

---

## Exporting to PDF (Chrome / Chromium)

1. Open [https://devops-cloud-courses.github.io/dockerkube/?print-pdf&pdfSeparateFragments=false](https://devops-cloud-courses.github.io/dockerkube/?print-pdf&pdfSeparateFragments=false)
2. Press `Cmd + P` (macOS) or `Ctrl + P` (Windows/Linux)
3. Configure the print dialog settings:

| Setting | Value |
|---|---|
| **Destination** | Save as PDF |
| **Pages** | All |
| **Margins** | None / Default |
| **Options** | Enable **Background graphics** |

---

## Authors

* **Laurent Grangeau** — Cloud Solution Architect @ Google ([@laurentgrangeau](https://twitter.com/laurentgrangeau) | [github.com/laurentgrangeau](https://github.com/laurentgrangeau))
* **Arthur Mauvezin** — DevOps Engineer @ Ouidou Consulting ([@arthurmauvezin](https://twitter.com/arthurmauvezin) | [github.com/arthurmauvezin](https://github.com/arthurmauvezin))
