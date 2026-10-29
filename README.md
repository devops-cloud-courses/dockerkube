# Docker & Kubernetes in a Nutshell

[![Live Presentation](https://img.shields.io/badge/Slides-Live%20Presentation-2563eb?style=for-the-badge&logo=github)](https://devops-cloud-courses.github.io/dockerkube/)
[![Reveal.js](https://img.shields.io/badge/Reveal.js-6.0.2-0f172a?style=for-the-badge)](https://revealjs.com/)

Presentation slides for the **Docker & Kubernetes in a Nutshell** course, introducing containerization internals, container build & runtime workflows, Kubernetes architecture, cloud-native networking, observability, and service meshes.

👉 **Live presentation:** [https://devops-cloud-courses.github.io/dockerkube/](https://devops-cloud-courses.github.io/dockerkube/)

---

## 📚 Course Outline

The presentation is organized into modular Markdown slide decks under [`slides/`](slides/):

| Section | File | Key Topics Covered |
| :--- | :--- | :--- |
| **1. Title & Speakers** | [`slides/title.md`](slides/title.md) | Course title & speaker introductions |
| **2. Introduction** | [`slides/introduction.md`](slides/introduction.md) | Interactive navigation guide & keyboard shortcuts |
| **3. Docker & Containers** | [`slides/docker.md`](slides/docker.md) | • Shipping container analogy & "Matrix from Hell"<br>• Virtual Machines vs. Containers<br>• Linux kernel primitives: **`cgroups`** & **`namespaces`**<br>• Docker Engine (`dockerd`, `containerd`, `runc`) & OCI specs<br>• Image layers (Copy-on-Write), `Dockerfile`, & multi-stage builds<br>• Container lifecycle CLI (`build`, `run`, `ps`, `exec`, `logs`, volumes, networks)<br>• **Docker Compose** (multi-container local stacks)<br>• **Podman** (daemonless, rootless, Pod-native alternative)<br>• **Container Security** best practices (non-root, distroless, CVE scanning)<br>• **CI/CD Pipeline** with containers & orchestrator comparison (Swarm vs. Kubernetes) |
| **4. Kubernetes** | [`slides/kubernetes.md`](slides/kubernetes.md) | • Monolith vs. Microservices & container orchestration challenges<br>• Control Plane (`kube-apiserver`, `etcd`, `kube-scheduler`, `kube-controller-manager`) & Worker Nodes (`kubelet`, `kube-proxy`, CRI)<br>• **Namespaces** & **Pods** (multi-container co-location, shared network/storage, YAML manifests)<br>• **Workload Controllers**: `Deployment`, `ReplicaSet`, `StatefulSet`, `DaemonSet`, `Job`, `CronJob`<br>• **Health Probes** (`startupProbe`, `readinessProbe`, `livenessProbe`) & zero-downtime **Rolling Updates**<br>• **Networking**: `Service` (`ClusterIP`, `NodePort`, `LoadBalancer`), `Ingress`, and **Gateway API (`HTTPRoute`)**<br>• **Configuration & Storage**: `ConfigMap`, `Secret`, `StorageClass`, `PersistentVolume`, `PersistentVolumeClaim`<br>• **Autoscaling**: `HorizontalPodAutoscaler` (HPA)<br>• **Packaging**: **Helm v3** (Charts, `values.yaml`, client-only architecture)<br>• **Monitoring**: Prometheus, Alertmanager, Grafana, OpenTelemetry |
| **5. Logging in Kubernetes** | [`slides/logging.md`](slides/logging.md) | • Node-level logging (`stdout` / `stderr` rotation by `kubelet`)<br>• DaemonSet node-level logging agents (**Fluent Bit**, **Vector**, **Grafana Alloy / OTel Collector**)<br>• Sidecar streaming & sidecar logging agent patterns<br>• Direct application log push |
| **6. Service Mesh** | [`slides/service-mesh.md`](slides/service-mesh.md) | • Why a Service Mesh? (mTLS, canary/traffic splitting, distributed tracing)<br>• Data Plane vs. Control Plane architecture (**Istio**, **Linkerd**, **Cilium**)<br>• **Sidecar** (per-Pod Envoy/`linkerd2-proxy`) vs. **Sidecarless** (Istio Ambient `ztunnel` & Cilium eBPF) |
| **7. Conclusion** | [`slides/conclusion.md`](slides/conclusion.md) | Q&A and wrap-up |

---

## ✨ Key Features & Technical Stack

- **Reveal.js `6.0.2`**: Loaded via jsDelivr CDN with the `Markdown`, `Highlight`, and `Notes` plugins—zero build step required.
- **Modern Light Theme with Dark Code Blocks**: Custom CSS design system ([`css/custom.css`](css/custom.css)) using `Inter` and `JetBrains Mono` typography, pairing clean light slides with high-contrast dark slate (`#0f172a`) code blocks and step-by-step line highlighting (`[1-4|5-9|...]`).
- **Self-Contained Custom Vector Diagrams**: 35 custom SVG architectural diagrams in [`img/`](img/) with unified styling, crisp scaling, and zero external image hotlinks.
- **Up-to-Date Cloud-Native Ecosystem**: Covers OCI standards, Podman rootless containers, Kubernetes Gateway API (`HTTPRoute`), Helm v3, OpenTelemetry / Grafana Alloy, and Ambient / eBPF sidecarless service meshes.

---

## 🚀 Running Locally

Because Reveal.js loads external Markdown files (`slides/*.md`) over HTTP, serve the repository root with any static HTTP server:

```bash
# Using Python 3
python3 -m http.server 8000
```

```bash
# Or using Node.js
npx serve .
```

Then open [http://localhost:8000](http://localhost:8000) in your browser.

### Keyboard Shortcuts

- <kbd>←</kbd> / <kbd>→</kbd>: Navigate between main sections
- <kbd>↑</kbd> / <kbd>↓</kbd>: Navigate vertically within a section (or step through highlighted code lines)
- <kbd>Space</kbd>: Next slide in linear lecture order
- <kbd>Esc</kbd> or <kbd>O</kbd>: Toggle slide overview mode
- <kbd>S</kbd>: Open speaker notes view
- <kbd>?</kbd>: Show help overlay

---

## 📁 Repository Structure

```text
.
├── index.html              # Reveal.js 6.0.2 entrypoint and configuration
├── css/
│   └── custom.css          # Custom light theme & high-contrast dark code block styles
├── slides/
│   ├── title.md            # Hero title slide & speaker bios
│   ├── introduction.md     # Presentation navigation instructions
│   ├── docker.md           # Docker & container fundamentals
│   ├── kubernetes.md       # Kubernetes architecture, workloads, networking, storage, Helm, monitoring
│   ├── logging.md          # Kubernetes logging architectures
│   ├── service-mesh.md     # Service Mesh (Sidecar vs. Ambient / eBPF)
│   └── conclusion.md       # Final Q&A slide
└── img/                    # 35 custom vector SVG diagrams & speaker avatars
```

---

## 👥 Authors

- **Laurent Grangeau** — Cloud Solution Architect @ Google ([@laurentgrangeau](https://github.com/laurentgrangeau))
- **Arthur Mauvezin** — DevOps Engineer @ Ouidou Consulting ([@arthurmauvezin](https://github.com/arthurmauvezin))
