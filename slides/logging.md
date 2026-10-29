## Logging

----

### Node-Level Logging

- The container runtime captures `STDOUT` / `STDERR` and writes rotated log files to `/var/log/pods` on the node

<img src="img/k8s-logging-node.svg" alt="Node Level Logging" width="1120" height="392" />

----

### Node Logging Agent (`DaemonSet`)

- A node-level logging agent (`Fluent Bit`, `Vector`, `Grafana Alloy`, `OTel Collector`) runs as a `DaemonSet` and ships logs to a central backend

<img src="img/k8s-logging-agent.svg" alt="Node Logging Agent" width="1120" height="379" />

----

### Streaming Sidecar Container

- When an app writes to custom log files instead of `STDOUT`, a sidecar container tails those files and streams them to `STDOUT`/`STDERR`

<img src="img/k8s-logging-sidecar-stream.svg" alt="Streaming Sidecar Logging" width="1120" height="383" />

----

### Sidecar with a Logging Agent

- A dedicated logging agent runs as a sidecar inside the Pod and pushes logs directly to the logging backend

<img src="img/k8s-logging-sidecar-agent.svg" alt="Sidecar Logging Agent" width="1120" height="379" />

----

### Direct Push from Application

- The application pushes structured logs directly over the network to a log management API (`OpenTelemetry`, `Datadog`, `Loki`)

<img src="img/k8s-logging-direct.svg" alt="Application Direct Logging" width="1120" height="392" />
