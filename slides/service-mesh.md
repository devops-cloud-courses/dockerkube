## Service Mesh

----

### Why a Service Mesh?

- While **Ingress / Gateway API** handles **North–South** traffic entering the cluster, a **Service Mesh** (`Istio`, `Linkerd`, `Cilium`) governs **East–West** communication between internal microservices
- Offloads cross-cutting network concerns from application code into infrastructure:
  - **Zero-Trust Security**: Automatic **Mutual TLS (`mTLS`)**, SPIFFE identity, and `L7` authorization policies
  - **Traffic Management**: Canary rollouts, weighted traffic splitting, retries, timeouts, and circuit breaking
  - **Observability**: Uniform `L7` golden signals (latency, traffic, errors) and distributed tracing across all services

----

### Service Mesh Architecture

<img src="img/service-mesh.svg" alt="Service Mesh Architecture" width="1120" height="499" />

----

### Sidecar vs. Sidecarless (`Ambient` / `eBPF`)

| Architecture | Data Plane Mechanism | Pros & Trade-offs |
| :--- | :--- | :--- |
| **Per-Pod Sidecar** (`Istio Classic`, `Linkerd`) | Injects an `Envoy` or `linkerd2-proxy` container into every application Pod | Strong isolation per Pod, but adds memory/CPU overhead and couples proxy upgrades to Pod restarts |
| **Sidecarless / Ambient** (`Istio Ambient`, `Cilium`) | Uses a shared per-Node `L4` proxy (`ztunnel`) + `eBPF` kernel hooks (and optional `L7` waypoint proxies) | Zero sidecar injection required, lower resource footprint, and transparent node-level `mTLS` |
