# Docker
```
cd $HOME
mkdir docker-class
cd docker-class
docker pull debian:latest
docker pull alpine:3.8
docker pull alpine:latest
docker pull httpd:2.4
docker pull golang:1.9.4
docker pull fedora:28
docker pull spkane/quantum-game:latest
docker pull spkane/outyet:1.9.4-small
docker pull spkane/train-os:latest
docker pull spkane/alpine-base:latest
git clone https://github.com/spkane/balance_game.git --config core.autocrlf=input
git clone https://github.com/spkane/outyet.git --config core.autocrlf=input
git clone https://github.com/spkane/docker-registry.git --config core.autocrlf=input
git clone https://github.com/spkane/docker-volumes-example.git --config core.autocrlf=input
wget https://raw.githubusercontent.com/spkane/train-os/master/default.json # optional
wget https://raw.githubusercontent.com/spkane/train-os/master/tcpdump.json # optional
alt to prev
wget https://raw.githubusercontent.com/spkane/train-os/master/tcpdump.json
```
https://gist.github.com/spkane/98ac0c87a019f9305c831966e4d5f0b8

```
docker-machine ip
usually 192.168.99.100
```
```
$ docker system prune -a
WARNING! This will remove:
  - all stopped containers
  - all networks not used by at least one container
  - all images without at least one container associated to them
  - all build cache
```

```
You can push a new image to this repository using the CLI
docker tag local-image:tagname new-repo:tagname
docker push new-repo:tagname
```
```
docker build -t vkku/balance_game .
--no-cache : for skipping build cache, give me a fresh copy
docker push vkku/balance_game:latest
docker search vkku
```

```
docker run -d --rm --name balance_game -p 18081:80 vkku/balance_game:latest
-ti : for interactive machine
/bin/sh should be present
```

Layering and Imaging on each of ```DockerFile``` statements
```tar -tvf export.zip```
```
docker tag [image_identifier]
docker history [tag]

```

```

```
---

# A Note for Windows Users
This class was written from a largely Unix based perspective, but everything can be made to work in Windows with very little effort.
* Unix Variables
  * `export MY_VAR=test`
  * `echo ${MY_VAR}`
* Windows 10 Variables (powershell)
  * `$env:my_var = "test"`
  * `Get-ChildItem Env:my_var`

---

# A Note About Proxies
Proxies can interfere with some Docker activities if they are not configured correctly.

If required, you can configure a proxy in Docker: Community Edition via the preferences.

---

# Instructor Environment
* **Operating System**: macOS (v10.15.X+)
* **Terminal**: iTerm2 (Build 3.X.X+) - https://www.iterm2.com/
* **Browser Extension**: Dark Reader - https://darkreader.org/
* **Shell Prompt Theme**: Starship - https://starship.rs/
* **Shell Prompt Font**: Fira Code - https://github.com/tonsky/FiraCode
* **Text Editor**: Visual Studio Code (v1.X.X+) - https://code.visualstudio.com/

---

# Docker in Translation

* **Docker client**
  * The docker command used to control most of the Docker workflow and talk to remote Docker servers.

* **Docker server**
  * The dockerd command used to launch the Docker daemon. This turns a Linux system into a Docker server that can have containers deployed, launched, and torn down via a remote client.

---

# Docker in Translation

* **Virtual Machine**
  * In general, the docker server can be only directly run on Linux. Because of this, it is common to utilize a Linux virtual machine to run Docker on other development platforms. Docker Community/Desktop Edition makes this very easy.

---

# Docker in Translation

* **Docker images**
  * Docker images consist of one or more filesystem layers and some important metadata that represent all the files required to run a Dockerized application. A single Docker image can be copied to numerous hosts. A container will typically have both a name and a tag. The tag is generally used to identify a particular release of an image.

---

# Docker in Translation

* **Linux Containers**
  * A Linux Container is a single instantiation of a Docker or OCI-standard image. A specific container can only exist once; however, you can easily create multiple containers from the same image.
  * OCI - Open Container Initiative

---

# Testing the Docker Setup

```shell
$ docker image ls
$ docker container run -d --rm --name quantum \
    --publish mode=ingress,target=8080,published=18080 \
    spkane/quantum-game:latest
$ docker container ls
```

* In a web browser, navigate to port 18080 on your Docker server.

```shell
$ docker container stop quantum
$ docker container ls
$ docker container ls -a
```

---

# Exploring the Dockerfile

```shell
$ cd ${HOME}
$ mkdir class-docker-images
$ cd ${HOME}/class-docker-images
$ git clone https://github.com/spkane/balance_game.git \
    --config core.autocrlf=input
$ cd balance_game
```

* Open & explore Dockerfile in your text editor

**Full Documentation**:
https://docs.docker.com/engine/reference/builder/

---

# Registering with Docker Hub

Create an account at: https://hub.docker.com/

```shell
$ docker login
$ cat ~/.docker/config.json
```

---

# Registry Authentication

```json
{
    "auths": {
        "https://index.docker.io/v1/": {
            "auth": "q378t348q7tb78bfs387b==",
            "email": "me@example.com"
        }
    }
}
```

---

# Create Your Image Repository

* **Login**:` https://hub.docker.com/`
* **Click**:` Create Repository+`
* **Enter name**: `balance_game`
* **Set visibility**: `public`
* **Click**: `Create`

---

# Building Your First Image

```shell
$ export HUB_USER=${USER}
$ docker image build -t ${HUB_USER}/balance_game:latest .
$ docker image push ${HUB_USER}/balance_game:latest
$ docker search ${HUB_USER}
$ docker container run -d --rm --name balance_game \
    --publish mode=ingress,target=80,published=18081 \
    ${HUB_USER}/balance_game:latest
$ docker container stop balance_game
```

---

# Docker Hub API Examples

```shell
$ curl -s -S \
  "https://registry.hub.docker.com/v2/repositories/library/alpine/tags/" \
  | jq '."results"[]["name"]' | sort
```

---

# A Typical Build Process

1. Client uploads build context to the server
1. Base image pulled, if required
1. New intermediate container created from base image
    * or empty container created, if using `FROM scratch`
1. Dockerfile command executed inside intermediate container
1. New image/layer created from intermediate container
1. If there is another step, a new intermediate container is created from the last step, and then the build goes back to step 3.

---

<!-- _class: lead -->

# Advanced Dockerfile Techniques

---

# Keep it Small

* Each and every layer’s size matters
* Don’t install unnecessary files

```shell
$ cd ${HOME}/class-docker-images
$ docker container run --rm -d --name outyet-small \
    --publish mode=ingress,target=18088,published=8090 \
    spkane/outyet:1.9.4-small
$ docker container export outyet-small -o export.tar
$ tar -tvf export.tar
$ docker container stop outyet-small
$ rm export.tar
```

---

# Debugging an Image

* If your image has a shell installed you can access it using a command like this:

```shell
$ docker image ls
$ docker container run --rm -ti spkane/outyet:1.9.4-small /bin/sh
```

But without a shell in the image this will fail.

---

# Debugging an Image

So, let's fix this:

```shell
$ git clone https://github.com/spkane/outyet.git \
    --config core.autocrlf=input
$ cd outyet
```

---

# Multi-Stage Images

```dockerfile
FROM golang:1.9.4

COPY . /go/src/outyet
WORKDIR /go/src/outyet

ENV CGO_ENABLED=0
ENV GOOS=linux

RUN go get -v -d && \
    go install -v && \
    go test -v && \
    go build -ldflags "-s" -a -installsuffix cgo -o outyet .
```

---

# Multi-Stage Images

```dockerfile
# To support debugging, let's use alpine instead of scratch
FROM alpine:latest

# Since we are using alpine we can simply install these
RUN apk --no-cache add ca-certificates

WORKDIR /
COPY --from=0 /go/src/outyet/outyet .

EXPOSE 18088

CMD ["/outyet", "-version", "1.9.4", "-poll", "600s", "-http", ":18088"]
```

---

# Building the Improved Image

```shell
$ docker image build -f Dockerfile -t outyet:1.9.4-local .
```

---

# Debugging an Image

* Now that we have a shell, let's try this again:

```shell
$ docker image ls
$ docker container run --rm -ti outyet:1.9.4-local /bin/sh
```

* Once inside the new container:

```shell
$ ls -lFa /outyet
$ exit
```

---

# Debugging a Broken Build (1 of 2)

* Break the `Dockerfile` and then try building it again.

```dockerfile
RUN apc --no-cache add ca-certificates
```

```shell
$ docker image build -f Dockerfile .
```

---

# Debugging a Broken Build (2 of 2)

* Let's debug the last successful image in that build

```shell
$ docker container run --rm -ti ${IMAGE_ID} /bin/sh
```

* Once inside the new container:

```shell
$ apc --no-cache add ca-certificates
$ apk --no-cache add ca-certificates
$ exit
```

---

# Smart Layering

* Each and every layer’s size matters
* Clean up, inside of each step.

```shell
$ cd ${HOME}/class-docker-images
$ cd balance_game
$ docker image build -f Dockerfile.fedora .
$ docker image tag ${IMAGE_ID} size${#}
$ docker image history size${#}
```

---

# Smart Layering

* edit `Dockerfile.fedora`, build, and re-examine size

```shell
RUN dnf install -y httpd
RUN dnf clean all
```

```shell
RUN dnf install -y httpd && \
    dnf clean all
```

---

# Timing commands in Windows

* In the next exercise we will be timing commands using a Unix utility. If you are on Windows and want to try to time these commands locally, you can try something like this in Powershell.

```powershell
PS C:\> $t = Measure-Command { docker image build --no-cache . }
PS C:\> Write-Host That command took $t.TotalSeconds to complete.
```

---

# Order Matters

* Keep commands that change towards the end of your Dockerfile.

```shell
$ cd ${HOME}/class-docker-images
$ cd balance_game
$ time docker image build --no-cache .
$ time docker image build .
```

* edit `start.sh`

```shell
$ time docker image build .
```

---

# Order Matters

* Add to the top of Dockerfile:

```dockerfile
ADD start.sh /
```

* And then remove the same line from the bottom of the Dockerfile.

```shell
$ time docker image build --no-cache .
$ time docker image build .
$ vi start.sh
$ time docker image build .
```

---

# Private Registry

```shell
$ git clone https://github.com/spkane/docker-registry.git \
    --config core.autocrlf=input
$ cd docker-registry
$ docker-compose up -d
```

---

# Private Registry

```shell
$ docker login 127.0.0.1:5000
$ docker image ls registry
$ docker image tag $(docker image ls registry --format "{{.ID}}") \
    127.0.0.1:5000/registry
$ docker image push 127.0.0.1:5000/registry
$ docker-compose down
```

Docker Distribution Project:
https://github.com/docker/distribution

---

# What We Have Learned

* What a Dockerfile is
* Building a Docker image
* Using Docker Hub
* Keeping Images Small
* Keeping Builds Fast
* Multi-Stage Dockerfiles
* Debugging Images
* Creating a Private Registry

---

# Additional Reading

* The 12-Factor App
  * http://12factor.net/

* Official Docker Documentation
  * https://docs.docker.com/

* Docker: Up and Running
  * http://shop.oreilly.com/product/0636920153566.do

---
# The Slow Rise Of Linux Containers

* **chroot system call** (*1979*)
  * Version 7 Unix
* **jail** (*2000*)
  * FreeBSD 4.0
* **Solaris Zones** (*2004*)
  * Solaris 10
* **Linux Containers - LXC** (*2008*)
  * version 2.6.24 of the Linux kernel

   ---

# The Quick Rise of Docker

* Docker Engine - Announced March 15, 2013

* **Provided**:
  * Application packaging
    * App & dependencies packed together
    * Single deployment artifact
  * Abstract software from hardware without sacrificing resources

---

# Docker Engine isn’t a…

* virtualization platform (VMware, KVM, etc.)
* cloud platform (AWS, Azure, etc.)
* configuration management tool (Chef, Puppet, etc.)
* deployment framework (Capistrano, etc.)
* workload management tool (Mesos, Kubernetes, etc.)
* development environment (Vagrant, etc.)

---

# Testing the Docker Setup

```shell
$ docker image ls
$ docker container run -d --rm --name quantum \
    --publish mode=ingress,target=8080,published=18080 \
    spkane/quantum-game:latest
$ docker container ls
```

* In a web browser, navigate to port 18080 on your Docker server.

```shell
$ docker container stop quantum
$ docker container ls
$ docker container ls -a
```

---

# Network Ports

```shell
$ docker container run -d --rm --name quantum \
    --publish mode=ingress,target=8080,published=18090 \
    spkane/quantum-game:latest
```

* In a web browser, navigate to port 18090 on your Docker server.
  * (e.g.) http://127.0.0.1:18090/

```shell
$ docker container stop quantum
```

---

# Shared Storage

```shell
$ git clone https://github.com/spkane/docker-volumes-example.git \
    --config core.autocrlf=input
$ cd docker-volumes-example
$ docker image build -t volumes .
$ docker container run --rm \
    mode=ingress,target=80,published=18080 volumes
$ docker container run --rm \
    --publish mode=ingress,target=80,published=18080 -v \
    ${PWD}/volumes/single-file/1.jpg:/usr/local/apache2/htdocs/images/1.jpg \
    volumes
$ docker container run --rm \
    --publish mode=ingress,target=80,published=18080 -v \
    ${PWD}/volumes/directory:/usr/local/apache2/htdocs/images volumes
```

---

# Re-Launching Your Container

```shell
$ docker container run -d --rm --name quantum \
    --publish mode=ingress,target=8080,published=18080 \
    spkane/quantum-game:latest
```

---

# Accessing STDOUT/STDERR

* Log everything to STDOUT / STDERR
  * Avoid writing logs into file

```shell
$ docker container logs --help
$ docker container logs -f quantum
```

* Reload the game in your web browser.
  * (e.g.) http://127.0.0.1:18080/

```shell
$ docker container logs -t quantum
```

---

# Statistics

```shell
$ docker container stats --help
$ docker container run --rm -d --name stress spkane/train-os:latest \
    stress -v --cpu 2 --io 1 --vm 2 \
    --vm-bytes 128M --timeout 240s
$ docker container stats stress
$ docker container stats --no-stream stress
$ docker container top stress
```

---

# Statistics API

* Try the API:

```shell
$ curl --no-buffer -XGET --unix-socket /var/run/docker.sock \
    http://docker/containers/stress/stats
```

---

# Events

```shell
$ docker container stop stress
$ docker system events --help
$ docker system events --since $(date +%Y%m%d)
```

* Try the API:

```shell
$ curl --no-buffer -XGET --unix-socket /var/run/docker.sock \
    http://docker/events
```

---

# Debugging a Live Container

* If your container has a shell installed you can access it using a command like this:

```shell
$ docker container ls
$ docker container exec -ti quantum /bin/sh
# ps auxwww
# exit
```

---

# Linux Namespaces

* Mount (filesystem resources)
* UTS (host & domain name)
* IPC (shared memory, semaphores)
* PID (process tree)
* Network (network layer)
* User (user and group IDs)

---

# Control Groups (cgroups)

* Resource limiting
* Prioritization
* Accounting
* Control

---

# Stressing the System

```shell
$ docker container run --rm -ti spkane/train-os:latest stress \
    -v --cpu 2 --io 1 --vm 2 --vm-bytes 128M --timeout 240s
$ docker container run -it --pid=host spkane/alpine-base:latest sh
# htop -p $(pgrep stress | tr '\n' ',')
# exit
```

---

# CPU Quotas

```shell
$ docker container run -d --cpus="2" spkane/train-os:latest stress \
    -v --cpu 2 --io 1 --vm 2 --vm-bytes 128M --timeout 60s
$ docker container run -d --cpus=".25" spkane/train-os:latest stress \
    -v --cpu 2 --io 1 --vm 2 --vm-bytes 128M --timeout 60s
$ docker container run -d --cpus="8" spkane/train-os:latest stress \
    -v --cpu 2 --io 1 --vm 2 --vm-bytes 128M --timeout 60s
```

---

# Memory Quotas

```shell
$ docker container run --rm -ti --memory="512m" spkane/train-os:latest \
    stress -v --cpu 2 --io 1 --vm 2 \
    --vm-bytes 128M --timeout 10s
$ docker container run --rm -ti --memory="100m" spkane/train-os:latest \
    stress -v --cpu 2 --io 1 --vm 2 \
    --vm-bytes 128M --timeout 10s
$ docker container run -it  --privileged --pid=host debian \
    nsenter -t 1 -m -u -n -i sh
# dmesg
# exit
```

---

# Timing commands in Windows

* In the next exercise we will be timing commands using a Unix utility. If you are on Windows and want to try to time these commands locally, you can try something like this in Powershell.

```powershell
PS C:\> $t = Measure-Command `
    { docker container run -ti --rm spkane/train-os:latest `
    bonnie++ -u 500:500 -d /tmp -r 1024 -s 2048 -x 1 }
PS C:\> Write-Host That command took $t.TotalSeconds to complete.
```

---

# I/O Quotas

```shell
$ docker image pull spkane/train-os:latest
$ time docker container run -ti --rm spkane/train-os:latest bonnie++ \
    -u 500:500 -d /tmp -r 1024 -s 2048 -x 1
$ time docker container run -ti --rm --device-write-iops /dev/vda:256 \
    spkane/train-os:latest bonnie++ \
    -u 500:500 -d /tmp -r 1024 -s 2048 -x 1
$ time docker container run -ti --rm --device-write-bps /dev/vda:5mb \
    spkane/train-os:latest bonnie++ \
    -u 500:500 -d /tmp -r 1024 -s 2048 -x 1
```

---

# Container UID

```shell
$ docker container run spkane/train-os:latest whoami
$ docker container run -u 500 spkane/train-os:latest whoami
$ docker container run -u 99 spkane/train-os:latest whoami
$ docker container run -v /etc:/mnt/etc spkane/train-os:latest \
    cat /mnt/etc/docker/key.json
$ docker container run -v /etc:/mnt/etc -u 500 spkane/train-os:latest \
    cat /mnt/etc/docker/key.json
```

* Further Exploration: --userns-remap
  * https://docs.docker.com/engine/security/userns-remap/

---

# Privileged Containers

* **Warning:** Don't try this example on your system.

```shell
$ docker container run --rm -ti spkane/train-os:latest /bin/bash
# ip link ls
# ip link set eth0 address 02:0a:03:0b:04:0c
# exit
$ docker container run --rm -ti --privileged=true \
    spkane/train-os:latest /bin/bash
# ip link ls
# ip link set eth0 address 02:0a:03:0b:04:0c
# ip link ls
# exit
```

---

# The Danger of Privileges

```shell
$ docker container run -ti --rm spkane/train-os:latest \
    ntpdate 0.fedora.pool.ntp.org
$ docker container run -ti --rm --privileged=true spkane/train-os:latest \
    ntpdate 0.fedora.pool.ntp.org
$ docker container run -ti --rm --privileged=true spkane/train-os:latest \
    bash -c \
    "mount /dev/vda1 /mnt && ls -F /mnt/docker/volumes | head -n 10"
```

---

# Controlling Capabilities

```shell
$ docker container run -ti --rm --cap-add=SYS_TIME spkane/train-os:latest \
    ntpdate 0.fedora.pool.ntp.org
$ docker container run -ti --rm --cap-add=SYS_TIME spkane/train-os:latest \
    bash -c \
    "mount /dev/vda1 /mnt && ls -F /mnt/docker/volumes | head -n 10"
$ docker container run -ti --rm spkane/train-os:latest tcpdump -i eth0
$ docker container run -ti --rm --cap-drop=NET_RAW spkane/train-os:latest \
    tcpdump -i eth0
```

---

# Secure Computing Mode

```shell
$ docker container run -ti --rm --cap-drop=NET_RAW \
    spkane/train-os:latest strace tcpdump -i eth0 \
    | grep "Operation not permitted"
$ wget \
    https://raw.githubusercontent.com/spkane/train-os/master/default.json
$ wget \
    https://raw.githubusercontent.com/spkane/train-os/master/tcpdump.json
$ diff -u default.json tcpdump.json
$ docker container run -ti --rm --security-opt=seccomp:tcpdump.json \
    spkane/train-os:latest tcpdump -i eth0
```


---
# Networking
```
version: '3.8'
services:
  db:
    container_name: pg_container
    image: postgres
    restart: always
    environment:
      POSTGRES_USER: [USER]
      POSTGRES_PASSWORD: [PASSWORD]
      POSTGRES_DB: [DB_NAME]
      POSTGRES_HOST_AUTH_METHOD: trust
      PGDATA: /var/lib/postgresql/data/pgdata
    ports:
      - "5432:5432"
    volumes:
      - '~/mount/postgres:/var/lib/postgresql/data'
    network_mode: vkku-net
  pgadmin:
    container_name: pgadmin4_container
    image: dpage/pgadmin4
    restart: always
    environment:
      PGADMIN_DEFAULT_EMAIL: [EMAIL]
      PGADMIN_DEFAULT_PASSWORD: [PASSWORD]
    network_mode: vkku-net
    ports:
      - "5050:80"
```

---

# Capabilities & SECCOMP Mode

* Linux Kernel Capabilities:
  * http://man7.org/linux/man-pages/man7/capabilities.7.html
* Secure Computing Mode (Docker)
  * https://docs.docker.com/engine/security/seccomp/

---

# What We Have Learned

* History of Containers
* Container Logging
* Container Statistics
* Container Events
* Debugging Containers
* Kernel Functionality
* Controlling Resource Usage
* Improving Container Security

---
Book : Docker Up and Running
* Completed Ch 01
