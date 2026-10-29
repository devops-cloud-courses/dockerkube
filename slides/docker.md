## Docker

----

### The Challenge

<img src="img/the-challenge.png" alt="The Challenge" />

----

### The Matrix from Hell

<img src="img/the-matrix-from-hell.png" alt="The Matrix from Hell" />

----

### Cargo Transport Pre-1960

<img src="img/cargo-transport-pre-1960.png" alt="Cargo Transport Pre-1960" />

----

### Also a Matrix from Hell

<img src="img/also-a-matrix-from-hell.png" alt="Also a Matrix from Hell" />

----

### The Solution: Intermodal Shipping Container

<img src="img/intermodal-shipping-container.png" alt="Intermodal Shipping Container" />

----

### Docker: A Shipping Container for Code

<img src="img/shipping-container-for-code.png" alt="Shipping Container for Code" />

----

### Docker Eliminates the Matrix from Hell

<img src="img/eliminates-matrix-from-hell.png" alt="Eliminates Matrix from Hell" />

----

### A Brief History of Containers

* **[IBM VM/370 (1972)](https://en.wikipedia.org/wiki/VM_%28operating_system%29)** & **`chroot` (1979)** — Early hardware virtualization and filesystem isolation
* **[FreeBSD Jails (2000)](https://www.freebsd.org/cgi/man.cgi?query=jail&sektion=8&manpath=FreeBSD+4.0-RELEASE)** & **[Linux VServer (2001)](https://en.wikipedia.org/wiki/Linux-VServer)** — OS-level process sandboxing
* **[Solaris Containers (2004)](https://en.wikipedia.org/wiki/Solaris_Containers)** & **`LXC` (2008)** — Combining `cgroups` and `namespaces` in Linux
* **Docker (2013)** & **OCI Standard (2015)** — Standardized container images and runtime (`runc`, `containerd`)

> Containers have been around for **a very long time** — Docker made them accessible to every developer.

----

### Containers vs. Virtual Machines

<img src="img/containers-vs-vms.png" alt="Containers vs VMs" />

----

### Why Are Containers Lightweight?

<img src="img/why-are-containers-lightweight.png" alt="Why are containers lightweight" />

----

### Docker — Architecture & Workflow

<img src="img/basics-of-docker-system.png" alt="Docker Architecture" />

----

### Cgroups (Control Groups)

- **Control Groups (`cgroups v2`)** are a Linux kernel feature that **limits, accounts for, and isolates** hardware resource usage across process groups:
  - `CPU` shares and quotas
  - `Memory` and swap limits (triggers `OOM Killer` if exceeded)
  - `Disk I/O` and `Network` bandwidth throttling

----

### Cgroups — Unconstrained Processes

<img src="img/cgroups.png" alt="Cgroups unconstrained" />

<p class="ref-link"><a href="https://mairin.wordpress.com/2011/05/13/ideas-for-a-cgroups-ui/" target="_blank">More on cgroups</a></p>

----

### Cgroups — Resource Isolation

<img src="img/cgroups-usage.png" alt="Cgroups resource isolation" />

<p class="ref-link"><a href="https://mairin.wordpress.com/2011/05/13/ideas-for-a-cgroups-ui/" target="_blank">More on cgroups</a></p>

----

### Linux Namespaces (Part 1)

<p class="slide-subtitle">Kernel Isolation Primitives ("What a process can see")</p>

- **`pid` (Process ID)**
  - Isolates the process ID number space so a container sees its own `PID 1`
- **`net` (Network)**
  - Isolates network interfaces (`veth`), IP addresses, routing tables, port numbers, and `iptables` / `nftables` rules
- **`ipc` (Inter-Process Communication)**
  - Isolates System V IPC and POSIX message queues between containers

----

### Linux Namespaces (Part 2)

<p class="slide-subtitle">Filesystem, Hostname &amp; Privilege Isolation</p>

- **`mnt` (Mount)**
  - Isolates filesystem mount points, allowing each container to have its own root filesystem (`rootfs`)
- **`uts` (UNIX Time-Sharing)**
  - Isolates the hostname and NIS domain name per container
- **`user` & `cgroup`**
  - Maps root inside the container to an unprivileged host UID (`rootless` containers) and hides host cgroup hierarchies

----

### Namespaces — Process Tree View

<img src="img/namespaces.png" alt="PID Namespaces" />

----

### Image Layers & Copy-on-Write (`OverlayFS`)

<img src="img/container-layers.jpg" alt="Container Layers" />

----

### Container Image

- An **image** is an immutable, read-only template composed of stacked filesystem layers (`OCI Image Spec`)

```bash [1|3-8]
$ docker image ls

REPOSITORY   TAG       IMAGE ID       CREATED      SIZE
postgres     16        afe2b5e1859b   2 days ago   432MB
ubuntu       24.04     07c86167cdc4   4 days ago   78.1MB
redis        7-alpine  4f5f397d4b7c   5 days ago   41.2MB
nginx        1.27      ddd5c9c1d0f2   6 days ago   188MB
```

----

### Container

- A **container** is a runnable instance of an image: an isolated group of processes with a thin **read-write layer** on top of the read-only image layers

```bash [1|3-5]
$ docker ps

CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                  NAMES
4c01db0b339c   ubuntu:24.04   "bash"                   17 seconds ago   Up 16 seconds   3300-3310/tcp          webapp
d7886598dbe2   redis:7-alpine "docker-entrypoint.s…"   33 minutes ago   Up 33 minutes   0.0.0.0:6379->6379/tcp redis
```

----

### Building an Image (`Dockerfile`)

- A `Dockerfile` is a text file containing all the instructions needed to assemble a container image layer by layer

```dockerfile [1|2|3|4]
FROM ubuntu:24.04
COPY my_local_code /opt/myapp
RUN apt-get update && apt-get install -y --no-install-recommends python3 && rm -rf /var/lib/apt/lists/*
CMD ["python3", "-u", "/opt/myapp/main.py"]
```

----

### `docker build`

- The `Dockerfile` is interpreted by `docker build` (powered by **BuildKit**), caching unchanged layers automatically

```bash [1|2-6|7-9|10-12]
$ docker build -t myimage:latest .
[+] Building 4.2s (8/8) FINISHED                                 docker:default
 => [internal] load build definition from Dockerfile                       0.0s
 => [internal] load metadata for docker.io/library/ubuntu:24.04            0.4s
 => [internal] load build context                                          0.1s
 => [1/3] FROM docker.io/library/ubuntu:24.04@sha256:72297848456d5d37d126  0.8s
 => [2/3] COPY my_local_code /opt/myapp                                    0.1s
 => [3/3] RUN apt-get update && apt-get install -y python3                 2.6s
 => exporting to image                                                     0.2s
 => => exporting layers                                                    0.1s
 => => writing image sha256:d5933f3c9702a6694011a39f22aef4f1b91b           0.0s
 => => naming to docker.io/library/myimage:latest                          0.0s
```

----

### Running a Simple Container

- Starts an isolated process from `myimage`, executes its `CMD`, and exits when the main process (`PID 1`) finishes

```bash [1|2]
$ docker run --rm myimage:latest
Hello from my containerized app!
```

----

### Running an Interactive Container (`-it`)

- `-i` (`--interactive`) keeps `STDIN` open and `-t` (`--tty`) allocates a pseudo-terminal

```bash [1|2-6|7]
$ docker run -it debian:bookworm bash
Unable to find image 'debian:bookworm' locally
bookworm: Pulling from library/debian
05d1a5232b46: Pull complete
Digest: sha256:07fe888a6090482fc6e930c1282d1edf67998a39a09a0b339242fbfa2b602fff
Status: Downloaded newer image for debian:bookworm
root@d6c0fe130dba:/#
```

----

### Running a Detached Container (`-d`)

- `-d` (`--detach`) runs the container in the background and prints its container ID

```bash [1|2]
$ docker run -d nginx:1.27
96d899b862b6c2d265e8523335d8b1cb34cfff2a1d31c3253f431990d9c889a7
```

```bash [1|2-3]
$ docker ps
CONTAINER ID   IMAGE        COMMAND                  CREATED         STATUS         PORTS     NAMES
96d899b862b6   nginx:1.27   "/docker-entrypoint.…"   5 seconds ago   Up 4 seconds   80/tcp    stupefied_mclaren
```

----

### Environment Variables (`-e`)

- Environment variables configure containers at runtime without rebuilding the image (e.g., database credentials, feature flags, API endpoints)

```bash [1|2-3|4]
$ docker run -d \
    -e POSTGRES_USER='bar' \
    -e POSTGRES_PASSWORD='foo' \
    postgres:16
```

----

### Container Networking (`-p`)

- By default, containers run in an isolated network namespace. Use `-p <host_port>:<container_port>` to publish a container port on the host

```bash
$ docker run -d -p 8080:80 nginx:1.27
```

- Requests to `http://localhost:8080` on the host are forwarded to port `80` inside the container

----

### Data Persistence (`-v` / `--mount`)

- Container filesystems are ephemeral. To persist data beyond the container lifecycle, mount a **Docker Volume**

```bash
$ docker run -d -v myvol2:/app nginx:1.27
```

```json
"Mounts": [
    {
        "Type": "volume",
        "Name": "myvol2",
        "Source": "/var/lib/docker/volumes/myvol2/_data",
        "Destination": "/app",
        "Driver": "local",
        "RW": true
    }
]
```

----

### Container Logs (`docker logs`)

- Containers write application logs directly to standard output (`STDOUT` and `STDERR`), captured automatically by the Docker logging driver

```bash
$ docker run --name test -d busybox sh -c 'while true; do date; sleep 1; done'
```

```bash [1|2-4]
$ docker logs -f --tail 3 test
Mon Oct 29 16:40:00 UTC 2026
Mon Oct 29 16:40:01 UTC 2026
Mon Oct 29 16:40:02 UTC 2026
```

----

### Docker Compose (`compose.yaml`)

- **Docker Compose** defines and runs multi-container applications (services, networks, and volumes) declaratively in a single `compose.yaml` file

```yaml [1|2-11|12-22|23-24]
services:
  db:
    image: mysql:8.0
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
volumes:
  db_data:
```

----

### Common Use Cases — CI/CD Pipelines

- Every build, test, and security scan runs inside reproducible, ephemeral containers

<img src="img/cicd-containers.svg" alt="CI/CD with Containers" />

----

### Common Use Cases — Microservices

<img src="https://github.com/dockersamples/example-voting-app/raw/master/architecture.png" alt="Microservices Example Voting App" />
