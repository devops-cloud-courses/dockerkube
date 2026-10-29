## Kubernetes

----

### Container Orchestration

- Running a single container on a laptop is easy — running **hundreds of containers across a cluster of servers** requires a **Container Orchestrator**
- An orchestrator continuously reconciles the **desired state** with the **actual state**, guaranteeing that workloads stay healthy even when nodes are upgraded or fail

----

### Docker Swarm

- **Docker Swarm** is the orchestrator built directly into the Docker Engine (`docker swarm init`)
- Very simple to set up and operate for small-scale clusters
- However, it lacks the rich ecosystem, advanced scheduling, auto-scaling, and cloud-provider integrations required for large production workloads

----

### Docker Swarm Architecture

<img src="https://cdn-images-1.medium.com/max/1207/1*PYC_TkhBjc0vtaPjjrg6MQ.png" alt="Docker Swarm Architecture" />

----

### Kubernetes Architecture

<img src="img/k8s-architecture.svg" alt="Kubernetes Architecture" />

----

### Declarative YAML Manifest

- Every Kubernetes object is declared via four root fields: `apiVersion`, `kind`, `metadata`, and `spec`

```yaml [1-2|3-6|7-16]
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: web
spec:
  containers:
    - name: nginx
      image: nginx:1.27
      ports:
        - containerPort: 80
      resources:
        requests:
          cpu: "100m"
          memory: "128Mi"
```

----

### Pods

- A **Pod** is the smallest deployable unit in Kubernetes — it wraps one or more tightly coupled containers that share the **same IP address, network namespace, and storage volumes**

<img src="https://d33wubrfki0l68.cloudfront.net/5cb72d407cbe2755e581b6de757e0d81760d5b86/a9df9/docs/tutorials/kubernetes-basics/public/images/module_03_nodes.svg" alt="Kubernetes Pods" width="50%" />

----

### Deployments

* A **Deployment** manages stateless application Pods via `ReplicaSets`
* It is **declarative**: you specify the desired number of replicas and image version, and the Deployment controller continuously ensures that exact number of healthy Pods is running

<img src="https://d33wubrfki0l68.cloudfront.net/152c845f25df8e69dd24dd7b0836a289747e258a/4a1d2/docs/tutorials/kubernetes-basics/public/images/module_02_first_app.svg" alt="Kubernetes Deployment" width="45%" />

----

### Services

* Pods are mortal and their IP addresses change whenever they are recreated
* A **Service** provides a stable **Virtual IP (`ClusterIP`)**, DNS name, and port to reliably reach a set of Pods matching a label selector
* Acts as an internal `L4` load balancer across healthy Pod endpoints (`ClusterIP`, `NodePort`, `LoadBalancer`)

<img src="https://d33wubrfki0l68.cloudfront.net/cc38b0f3c0fd94e66495e3a4198f2096cdecd3d5/ace10/docs/tutorials/kubernetes-basics/public/images/module_04_services.svg" alt="Kubernetes Services" width="35%" />

----

### Ingress & Gateway API

- **Ingress** (and its modern successor, the **Gateway API**) exposes `HTTP` and `HTTPS` routes from outside the cluster to internal Services
- Managed by an Ingress/Gateway controller (`NGINX`, `Envoy`, `Traefik`, or Cloud Load Balancers)
- Provides `L7` host/path routing, **TLS/SSL termination**, and virtual hosting under a single external IP

----

### Horizontal Pod Autoscaler (`HPA`)

- The **Horizontal Pod Autoscaler** automatically scales the number of Pods in a `Deployment` or `StatefulSet` up or down
- Driven by observed `CPU` / `Memory` utilization (via `metrics-server`) or custom application metrics (e.g., requests per second, queue depth via `KEDA`)

----

### Cluster Autoscaler & Karpenter

- When new Pods cannot be scheduled because existing Worker Nodes are full (`Pending` state), the **Cluster Autoscaler** (or **Karpenter**) automatically provisions new nodes from the cloud provider
- When nodes are underutilized, it safely drains workloads and terminates excess nodes to reduce cloud costs

----

### DaemonSet

- A **DaemonSet** guarantees that all (or a subset of) Worker Nodes run **one copy of a Pod** automatically
- As nodes join the cluster, Pods are added to them; as nodes are removed, those Pods are garbage-collected
- Ideal for cluster-wide agents:
  - **Networking (`CNI`)**: `Cilium`, `Calico`
  - **Observability & Logging**: `Datadog Agent`, `Fluent Bit`, `Prometheus Node Exporter`
  - **Storage (`CSI`)**: `Ceph`, `Longhorn`

----

### StorageClass

- A **StorageClass** defines different tiers ("classes") of storage offered by cluster administrators
- Maps to quality-of-service levels (`SSD` vs. `HDD`, IOPS, replication), backup policies, and a **CSI (`Container Storage Interface`)** provisioner (`AWS EBS`, `GCP Persistent Disk`, `Azure Disk`)

----

### PersistentVolume (`PV`)

- A **PersistentVolume (`PV`)** is a piece of storage in the cluster that has been provisioned either statically by an administrator or **dynamically** using a `StorageClass`
- Its lifecycle is completely independent of any individual Pod that uses it
- Backed by CSI storage plugins (`Cloud Block Storage`, `NFS`, `Ceph`, `iSCSI`)

----

### PersistentVolumeClaim (`PVC`)

- A **PersistentVolumeClaim (`PVC`)** is a request for storage by a user/Pod (specifying size, access mode like `ReadWriteOnce` or `ReadWriteMany`, and `StorageClassName`)
- Kubernetes automatically binds the `PVC` to a matching `PV` and mounts it into the Pod's containers

----

### ConfigMap

- A **ConfigMap** decouples non-confidential configuration data (key-value pairs or config files) from container images so artifacts remain portable across environments

```yaml [1-4|5-8]
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-redis-config
  namespace: default
data:
  redis-config: |
    maxmemory 2mb
    maxmemory-policy allkeys-lru
```

----

### Secret

- A **Secret** stores sensitive data such as passwords, OAuth tokens, and TLS keys so they are not hardcoded in `Pod` specs or container images
- Mounted into Pods as in-memory files (`tmpfs`) or injected as environment variables

```yaml [1-5|6-8]
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=
  password: MWYyZDFlMmU2N2Rm
```

----

### Rolling Updates (1/4)

<p class="slide-subtitle">Initial State — v1 Pods Serving Traffic</p>

<img src="https://d33wubrfki0l68.cloudfront.net/30f75140a581110443397192d70a4cdb37df7bfc/fa906/docs/tutorials/kubernetes-basics/public/images/module_06_rollingupdates1.svg" alt="Rolling Update Step 1" width="70%" />

----

### Rolling Updates (2/4)

<p class="slide-subtitle">Scale Up New ReplicaSet — First v2 Pod Created</p>

<img src="https://d33wubrfki0l68.cloudfront.net/678bcc3281bfcc588e87c73ffdc73c7a8380aca9/703a2/docs/tutorials/kubernetes-basics/public/images/module_06_rollingupdates2.svg" alt="Rolling Update Step 2" width="70%" />

----

### Rolling Updates (3/4)

<p class="slide-subtitle">Progressive Replacement — Traffic Shifts to Ready v2 Pods</p>

<img src="https://d33wubrfki0l68.cloudfront.net/9b57c000ea41aca21842da9e1d596cf22f1b9561/91786/docs/tutorials/kubernetes-basics/public/images/module_06_rollingupdates3.svg" alt="Rolling Update Step 3" width="70%" />

----

### Rolling Updates (4/4)

<p class="slide-subtitle">Rollout Complete — Zero Downtime Achieved</p>

<img src="https://d33wubrfki0l68.cloudfront.net/6d8bc1ebb4dc67051242bc828d3ae849dbeedb93/fbfa8/docs/tutorials/kubernetes-basics/public/images/module_06_rollingupdates4.svg" alt="Rolling Update Step 4" width="70%" />

----

### Liveness Probe

- **Liveness Probes** let `kubelet` know when a container is deadlocked or unhealthy and **must be restarted**

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5
```

----

### Readiness Probe

- **Readiness Probes** let `kubelet` and `Services` know when a container is **ready to accept traffic** (unready Pods are temporarily removed from Service load balancers)

```yaml
readinessProbe:
  httpGet:
    path: /readyz
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5
```

----

### Logging — Node Level

- The container runtime captures `STDOUT` / `STDERR` and writes rotated log files to `/var/log/pods` on the node

<img src="https://d33wubrfki0l68.cloudfront.net/59b1aae2adcfe4f06270b99a2789012ed64bec1f/4d0ad/images/docs/user-guide/logging/logging-node-level.png" alt="Node Level Logging" />

----

### Logging — Node Logging Agent (`DaemonSet`)

- A node-level logging agent (`Fluent Bit`, `Vector`, `Promtail`) runs as a `DaemonSet` and ships logs to a central backend

<img src="https://d33wubrfki0l68.cloudfront.net/2585cf9757d316b9030cf36d6a4e6b8ea7eedf5a/1509f/images/docs/user-guide/logging/logging-with-node-agent.png" alt="Node Logging Agent" />

----

### Logging — Streaming Sidecar Container

- When an app writes to custom log files instead of `STDOUT`, a sidecar container tails those files and streams them to `STDOUT`/`STDERR`

<img src="https://d33wubrfki0l68.cloudfront.net/c51467e219320fdd46ab1acb40867b79a58d37af/b5414/images/docs/user-guide/logging/logging-with-streaming-sidecar.png" alt="Streaming Sidecar Logging" />

----

### Logging — Sidecar with a Logging Agent

- A dedicated logging agent runs as a sidecar inside the Pod and pushes logs directly to the logging backend

<img src="https://d33wubrfki0l68.cloudfront.net/d55c404912a21223392e7d1a5a1741bda283f3df/c0397/images/docs/user-guide/logging/logging-with-sidecar-agent.png" alt="Sidecar Logging Agent" />

----

### Logging — Direct Push from Application

- The application pushes structured logs directly over the network to a log management API (`OpenTelemetry`, `Datadog`, `Loki`)

<img src="https://d33wubrfki0l68.cloudfront.net/0b4444914e56a3049a54c16b44f1a6619c0b198e/260e4/images/docs/user-guide/logging/logging-from-application.png" alt="Application Direct Logging" />

----

### Monitoring & Alerting

- Cloud-native monitoring relies on **Prometheus**, **Grafana**, and **OpenTelemetry** to scrape metrics from nodes, control-plane components, and application `/metrics` endpoints
- **Alertmanager** routes alerts to `Slack`, `PagerDuty`, or `Opsgenie` when SLOs are breached

----

### Monitoring & Alerting Architecture

<img src="https://camo.githubusercontent.com/37141c73c72e1e73ec0669ae6451d834ebe1cd2e/68747470733a2f2f7777772e63616d696c2e6f72672f636f6e74656e742f696d616765732f323031372f636c75737465722e706e67" alt="Prometheus Monitoring Architecture" />

----

### Managed Cloud Kubernetes

- Every major Cloud Service Provider offers a fully managed Kubernetes control plane:
  - **Google Cloud**: `GKE` (Google Kubernetes Engine — Standard & Autopilot)
  - **AWS**: `EKS` (Elastic Kubernetes Service)
  - **Microsoft Azure**: `AKS` (Azure Kubernetes Service)
  - **European Cloud Providers**: Scaleway `Kapsule`, OVHcloud `MKS`

----

### Helm — The Kubernetes Package Manager

- **Helm** is the package manager for Kubernetes — the equivalent of `apt`, `brew`, or `yum` for cloud-native applications
- Packages templated Kubernetes manifests into reusable **Charts** (`Chart.yaml`, `values.yaml`, `templates/`) distributed via HTTP or `OCI` registries
- **Helm v3** is purely client-side (no in-cluster `Tiller` daemon) and respects native Kubernetes `RBAC`

----

### Helm v3 Architecture

<img src="img/helm-v3-architecture.svg" alt="Helm v3 Architecture" />

----

### Service Mesh

- A **Service Mesh** (`Istio`, `Linkerd`, `Cilium`) manages **East–West** communication between microservices transparently without changing application code
- Provides **strict Mutual TLS (`mTLS`)**, canary traffic splitting, automatic retries, circuit breaking, and distributed tracing

----

### Service Mesh Architecture

<img src="img/service-mesh.svg" alt="Service Mesh Architecture" />
