## Kubernetes

----

### Container Orchestration

- Running a single container on a laptop is easy — running **hundreds of containers across a cluster of servers** requires a **Container Orchestrator**
- An orchestrator continuously reconciles the **desired state** with the **actual state**, guaranteeing that workloads stay healthy even when nodes are upgraded or fail

----

### Docker Swarm

- **Docker Swarm** is the orchestrator built directly into the Docker Engine (`docker swarm init`)
- Very simple to set up and operate for small-scale clusters
- While not officially deprecated, it is now largely **legacy** — superseded by **Kubernetes**, which provides the rich ecosystem, auto-scaling, and cloud-provider integrations required in production

----

### Docker Swarm Architecture

<img src="img/docker-swarm.svg" alt="Docker Swarm Architecture" width="1120" height="492" />

----

### Kubernetes Architecture

<img src="img/k8s-architecture.svg" alt="Kubernetes Architecture" width="1120" height="498" />

----

### Declarative YAML Manifest Structure

- Every Kubernetes resource is declared using four required root fields:
  - **`apiVersion`** — Which version of the Kubernetes API defines this object (`v1`, `apps/v1`, `gateway.networking.k8s.io/v1`)
  - **`kind`** — What type of object you want to create (`Pod`, `Deployment`, `Service`, `HTTPRoute`)
  - **`metadata`** — Data that uniquely identifies the object (`name`, `namespace`, `labels`, `annotations`)
  - **`spec`** — The **desired state** for the object (containers, replicas, ports, routing rules)

----

### Pods

- A **Pod** is the smallest deployable unit in Kubernetes — it wraps one or more tightly coupled containers that share the **same IP address, network namespace, and storage volumes**

<img src="img/k8s-pod.svg" alt="Kubernetes Pods" width="1120" height="419" />

----

### Pod — Manifest Example

```yaml [1-2|3-6|7-16]
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: web
spec:
  containers:
    - name: nginx
      image: nginx:1.28
      ports:
        - containerPort: 80
      resources:
        requests:
          cpu: "100m"
          memory: "128Mi"
```

----

### Deployments

* A **Deployment** manages stateless application Pods via `ReplicaSets`
* It is **declarative**: you specify the desired number of replicas and image version, and the Deployment controller continuously ensures that exact number of healthy Pods is running

<img src="img/k8s-deployment.svg" alt="Kubernetes Deployment" width="1120" height="383" />

----

### Deployment — Manifest Example

```yaml [1-4|5-9|10-18]
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: nginx
          image: nginx:1.28
          ports:
            - containerPort: 80
```

----

### DaemonSet

- A **DaemonSet** guarantees that all (or a subset of) Worker Nodes run **one copy of a Pod** automatically
- As nodes join the cluster, Pods are added to them; as nodes are removed, those Pods are garbage-collected
- Ideal for cluster-wide agents:
  - **Networking (`CNI`)**: `Cilium`, `Calico`
  - **Observability & Logging**: `Datadog Agent`, `Fluent Bit`, `Prometheus Node Exporter`
  - **Storage (`CSI`)**: `Ceph`, `Longhorn`

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

### Rolling Updates (1/4)

<p class="slide-subtitle">Initial State — v1 Pods Serving Traffic</p>

<img src="img/k8s-rolling-update-1.svg" alt="Rolling Update Step 1" width="1120" height="430" />

----

### Rolling Updates (2/4)

<p class="slide-subtitle">Scale Up New ReplicaSet — First v2 Pod Created</p>

<img src="img/k8s-rolling-update-2.svg" alt="Rolling Update Step 2" width="1120" height="402" />

----

### Rolling Updates (3/4)

<p class="slide-subtitle">Progressive Replacement — Traffic Shifts to Ready v2 Pods</p>

<img src="img/k8s-rolling-update-3.svg" alt="Rolling Update Step 3" width="1120" height="402" />

----

### Rolling Updates (4/4)

<p class="slide-subtitle">Rollout Complete — Zero Downtime Achieved</p>

<img src="img/k8s-rolling-update-4.svg" alt="Rolling Update Step 4" width="1120" height="402" />

----

### Services

* Pods are mortal and their IP addresses change whenever they are recreated
* A **Service** provides a stable **Virtual IP (`ClusterIP`)**, DNS name, and port to reliably reach a set of Pods matching a label selector
* Acts as an internal `L4` load balancer across healthy Pod endpoints (`ClusterIP`, `NodePort`, `LoadBalancer`)

<img src="img/k8s-service.svg" alt="Kubernetes Services" width="1120" height="379" />

----

### Service — Manifest Example

```yaml [1-4|5-8|9-13]
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  type: ClusterIP
  selector:
    app: web
  ports:
    - name: http
      protocol: TCP
      port: 80
      targetPort: 80
```

----

### Ingress & Gateway API

- Exposes `HTTP` and `HTTPS` routes from outside the cluster to internal Services (`L7` host/path routing, **TLS termination**, traffic splitting)
- **Ingress (`networking.k8s.io/v1`)**: The classic Kubernetes routing API — now feature-frozen (`ingress-nginx` retired by SIG Network)
- **Gateway API (`gateway.networking.k8s.io/v1`)**: The modern, role-oriented standard (`GatewayClass`, `Gateway`, `HTTPRoute`) backed by `Envoy Gateway`, `Cilium`, `Traefik`, `NGINX Gateway Fabric`, and Cloud LBs

----

### Ingress — Manifest Example (Legacy `v1`)

```yaml [1-4|5-9|10-19]
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-ingress
spec:
  ingressClassName: traefik
  tls:
    - hosts: [app.example.com]
      secretName: app-tls-cert
  rules:
    - host: app.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: web-service
                port:
                  number: 80
```

----

### Gateway API (`HTTPRoute`) — Manifest Example

```yaml [1-4|5-9|10-18]
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: web-route
spec:
  parentRefs:
    - name: prod-gateway
  hostnames:
    - "app.example.com"
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /
      backendRefs:
        - name: web-service
          port: 80
```

----

### Service Mesh

- A **Service Mesh** (`Istio`, `Linkerd`, `Cilium`) manages **East–West** communication between microservices transparently without changing application code
- Provides **strict Mutual TLS (`mTLS`)**, canary traffic splitting, automatic retries, circuit breaking, and distributed tracing
- Deployed via **per-Pod sidecars** (`Envoy`, `linkerd2-proxy`) or modern **sidecarless / ambient node proxies** (`Istio Ambient Mode ztunnel`, `Cilium eBPF`)

----

### Service Mesh Architecture

<img src="img/service-mesh.svg" alt="Service Mesh Architecture" width="1120" height="499" />

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

### Horizontal Pod Autoscaler (`HPA`)

- The **Horizontal Pod Autoscaler** automatically scales the number of Pods in a `Deployment` or `StatefulSet` up or down
- Driven by observed `CPU` / `Memory` utilization (via `metrics-server`) or custom application metrics (e.g., requests per second, queue depth via `KEDA`)

----

### Cluster Autoscaler & Karpenter

- When new Pods cannot be scheduled because existing Worker Nodes are full (`Pending` state), the **Cluster Autoscaler** (or **Karpenter**) automatically provisions new nodes from the cloud provider
- When nodes are underutilized, it safely drains workloads and terminates excess nodes to reduce cloud costs

----

### Helm — The Kubernetes Package Manager

- **Helm** is the package manager for Kubernetes — the equivalent of `apt`, `brew`, or `yum` for cloud-native applications
- Packages templated Kubernetes manifests into reusable **Charts** (`Chart.yaml`, `values.yaml`, `templates/`) distributed via `OCI` registries (`oci://`) or HTTP repos
- **Client-only architecture** (no in-cluster `Tiller` daemon): renders templates locally and applies them directly via the Kubernetes API using native `RBAC`

----

### Helm Architecture

<img src="img/helm-v3-architecture.svg" alt="Helm Architecture" width="1120" height="454" />

----

### Managed Cloud Kubernetes

- Every major Cloud Service Provider offers a fully managed Kubernetes control plane:
  - **Google Cloud**: `GKE` (Google Kubernetes Engine — Standard & Autopilot)
  - **AWS**: `EKS` (Elastic Kubernetes Service)
  - **Microsoft Azure**: `AKS` (Azure Kubernetes Service)
  - **European Cloud Providers**: Scaleway `Kapsule`, OVHcloud `MKS`

----

### Monitoring & Alerting

- Cloud-native monitoring relies on **Prometheus**, **Grafana**, and **OpenTelemetry** to scrape metrics from nodes, control-plane components, and application `/metrics` endpoints
- **Alertmanager** routes alerts to `Slack`, `PagerDuty`, `Grafana OnCall`, or `incident.io` when SLOs are breached

----

### Monitoring & Alerting Architecture

<img src="img/k8s-monitoring.svg" alt="Prometheus Monitoring Architecture" width="1120" height="451" />
