## Docker

----

### Le challenge

<img src="img/the-challenge.png" style="background:none; border:none; box-shadow:none;"/>

----

### La matrice de l'enfer

<img src="img/the-matrix-from-hell.png" style="background:none; border:none; box-shadow:none;"/>

----

### Le transport par cargo avant 1960

<img src="img/cargo-transport-pre-1960.png" style="background:none; border:none; box-shadow:none;"/>

----

### Aussi une matrice de l'enfer

<img src="img/also-a-matrix-from-hell.png" style="background:none; border:none; box-shadow:none;"/>

----

### La solution : le conteneur intermodal

<img src="img/intermodal-shipping-container.png" style="background:none; border:none; box-shadow:none;"/>

----

### Docker est un conteneur pour le code

<img src="img/shipping-container-for-code.png" style="background:none; border:none; box-shadow:none;"/>

----

### Docker résout la matrice de l'enfer

<img src="img/eliminates-matrix-from-hell.png" style="background:none; border:none; box-shadow:none;" width="80%"/>

----

### Un peu d'histoire

* [IBM VM/370 (1972)](https://en.wikipedia.org/wiki/VM_%28operating_system%29)

* [Linux VServers (2001)](http://www.solucorp.qc.ca/changes.hc?projet=vserver)

* [Solaris Containers (2004)](https://en.wikipedia.org/wiki/Solaris_Containers)

* [FreeBSD jails (1999-2000)](https://www.freebsd.org/cgi/man.cgi?query=jail&sektion=8&manpath=FreeBSD+4.0-RELEASE)

Les containers sont la depuis *très longtemps*

----

### Containers vs. VMs

<img src="img/containers-vs-vms.png" style="background:none; border:none; box-shadow:none;"/>

----

### Pourquoi les containers sont légers ?

<img src="img/why-are-containers-lightweight.png" style="background:none; border:none; box-shadow:none;"/>

----

### Docker - Worflow

<img src="img/basics-of-docker-system.png" style="background:none; border:none; box-shadow:none;"/>

----

### Cgroups (control groups)

cgroups (control groups) est une fonctionnalité du noyau Linux pour limiter, compter et isoler l'utilisation des ressources (processeur, mémoire, utilisation disque, etc.)

----

### Cgroups (control groups)

<img src="img/cgroups.png" style="background:none; border:none; box-shadow:none;"/>

[more on cgroups](https://mairin.wordpress.com/2011/05/13/ideas-for-a-cgroups-ui/)

----

### Cgroups - multiple cgroups

<img src="img/cgroups-usage.png" style="background:none; border:none; box-shadow:none;"/>

[more on cgroups](https://mairin.wordpress.com/2011/05/13/ideas-for-a-cgroups-ui/)

----

### Namespaces

- PID
  - Fournit l'isolation pour l'allocation des identifiants de processus (PIDs), la liste des processus et de leurs détails
- Network
  - Isole le contrôleur de l'interface réseau (physique ou virtuel), les règles de pare-feu iptables, les tables de routage, etc.
- IPC
  - Isole le System V de communication inter-processus entre les espaces de nommage

----

### Namespaces

- Mount
  - Permet de créer différents modèles de systèmes de fichiers, ou de créer certains points de montage en lecture-seule
- UTS
  - Permet le changement de nom d'hôte
- User
  - Isole les privilèges et sépare l'identification des utilisateurs dans plusieurs ensembles de processus

----

### Namespaces

<img src="img/namespaces.png" style="background:none; border:none; box-shadow:none;" width="60%" />

----

### Layer

<img src="img/container-layers.jpg" style="background:none; border:none; box-shadow:none;"/>

----

### Image

Une image est un système de fichier en lecture seule

```bash [1|2-4|5-6|7-8]
$ docker image ls

REPOSITORY      TAG       IMAGE ID       CREATED        SIZE
fedora          latest    ddd5c9c1d0f2   3 days ago     204.7 MB
centos          latest    d0e7f81ca65c   3 days ago     196.6 MB
ubuntu          latest    07c86167cdc4   4 days ago     188 MB
redis           latest    4f5f397d4b7c   5 days ago     177.6 MB
postgres        latest    afe2b5e1859b   5 days ago     264.5 MB
```

----

### Container

Un container est un ensemble de processus isolés qui s'exécute sur ce système de fichier

```bash [1|2-4|5]
$ docker ps

CONTAINER ID        IMAGE                        COMMAND                CREATED              STATUS              PORTS               NAMES
4c01db0b339c        ubuntu:12.04                 bash                   17 seconds ago       Up 16 seconds       3300-3310/tcp       webapp
d7886598dbe2        crosbymichael/redis:latest   /redis-server --dir    33 minutes ago       Up 33 minutes       6379/tcp            redis,webapp/db
```

----

### Création d'une image

Un Dockerfile est un fichier qui contient toutes les instructions nécessaires pour créer une container image

```dockerfile [1|2|3|4]
FROM ubuntu:18.04
COPY my_local_code /opt/myapp
RUN apt update -y && apt install -y python3
CMD ["python3", "-u", "/opt/myapp/main.py"]
```

----

### Docker build

Le fichier Dockerfile est interprêté par la commande docker build

```bash [1|2|3-8|9-10|11-22|23-24|25-28]
$ docker build -t monimage .
Sending build context to Docker daemon  44.15MB
Step 1/4 : FROM ubuntu:18.04
18.04: Pulling from library/ubuntu
e706e0a9f423: Pull complete 
Digest: sha256:50e71ab17a4bfbb11ec9c93aec3fca0b3acad79b4155b717842f1983cb42039e
Status: Downloaded newer image for ubuntu:18.04
 ---> 71cb16d32be4
Step 2/4 : COPY my_local_code /opt/myapp
 ---> 22aef4f1b91b
Step 3/4 : RUN apt update -y && apt install -y python3
 ---> Running in 7a294422b1e8
Get:1 http://security.ubuntu.com/ubuntu bionic-security InRelease [88.7 kB]
Get:2 http://archive.ubuntu.com/ubuntu bionic InRelease [242 kB]
Get:3 http://archive.ubuntu.com/ubuntu bionic-updates InRelease [88.7 kB]
[...]
Setting up python3 (3.6.7-1~18.04) ...
running python rtupdate hooks for python3.6...
running python post-rtupdate hooks for python3.6...
Processing triggers for libc-bin (2.27-3ubuntu1.6) ...
Removing intermediate container 7a294422b1e8
 ---> a6694011a39f
Step 4/4 : CMD ["python3", "-u", "/opt/myapp/main.py"]
 ---> Running in 190ee0a44836
Removing intermediate container 190ee0a44836
 ---> d5933f3c9702
Successfully built d5933f3c9702
Successfully tagged monimage:latest
```

----

### Démarrer un container simple 

```bash [1|2]
$ docker run monimage
Mon image 
```

----

### Démarrer un container en mode interactif

```bash [1|2-6|7]
$ docker run --name interactif -it debian bash
Unable to find image 'debian:latest' locally
latest: Pulling from library/debian
05d1a5232b46: Pull complete 
Digest: sha256:07fe888a6090482fc6e930c1282d1edf67998a39a09a0b339242fbfa2b602fff
Status: Downloaded newer image for debian:latest
root@d6c0fe130dba:/
```

----

### Démarrer un container en mode détaché

```bash [1|2]
$ docker run -d nginx
96d899b862b6c2d265e8523335d8b1cb34cfff2a1d31c3253f431990d9c889a7
```

```bash [1|2-3]
$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
96d899b862b6   nginx     "/docker-entrypoint.…"   5 seconds ago   Up 4 seconds   80/tcp    stupefied_mclaren
```

----

### Les variables d'environnement

Les variables d'environnements servent à paramétrer un container, par exemple pour mettre en place une chaine de connexion, ou un paramétrage particulier

```bash
$ docker run -d \
    -e POSTGRES_ENV_POSTGRES_USER='bar' \
    -e POSTGRES_ENV_POSTGRES_PASSWORD='foo' \
    postgres
```

----

### Le réseau

Pour accéder au service tournant dans le container, il faut passer mapper le port de la machine avec le service exposé

```bash
$ docker run --name nginx -p 8080:80 -d nginx
```

----

### La persistance

Pour pouvoir persister des données provenant des containers, il est nécessaire de passer par des volumes

```bash
$ docker run -d --name devtest -v myvol2:/app nginx:latest
```

```bash
"Mounts": [
    {
        "Type": "volume",
        "Name": "myvol2",
        "Source": "/var/lib/docker/volumes/myvol2/_data",
        "Destination": "/app",
        "Driver": "local",
        "Mode": "",
        "RW": true,
        "Propagation": ""
    }
]
```

----

### Les fichiers de log

Il est souvent nécessaire de voir ce qui se passe dans le container, notamment à des fins de monitoring et de debug

Par défaut, Docker écrit sur la sortie standard STDOUT

```bash
$ docker run --name test -d busybox sh -c "while true; do $(echo date); sleep 1; done"
```

```bash
$ date
Tue 14 Nov 2017 16:40:00 CET
```

```bash
$ docker logs -f --until=2s
Tue 14 Nov 2017 16:40:00 CET
Tue 14 Nov 2017 16:40:01 CET
Tue 14 Nov 2017 16:40:02 CET
```

----

### Docker compose

Pour simplifier toutes les notions vues précédemment, Docker utilise un fichier YAML, ainsi qu'un outil afin de décrire une stack logicielle complète

```yaml
version: '3'

services:
   db:
     image: mysql:5.7
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

### Quelques cas d'usage
#### CI/CD
<img src="http://codethebuild.github.io/slides/images/build-env-jenkins-containers.png" style="background:none; border:none; box-shadow:none;"/>

----

### Quelques cas d'usage
#### Microservices
<img src="https://github.com/dockersamples/example-voting-app/raw/master/architecture.png" style="background:none; border:none; box-shadow:none;" width="70%" />
