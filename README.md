# docker-training

Containerization - technology for providing multiple isolated environments on a single host

* [Docker CLI](#docker-cli)
* [Docker CLI for Images](#docker-cli-images)
* [Docker CLI for Containers](#docker-cli-containers)
* [History](#history)
* [Installation](#installation)
* [Docker Engine](#docker-engine)
* [Docker Images](#docker-images)
* [Docker Containers](#docker-containers)
* [Dockerfile](#dockerfile)
* [Docker Compose](#docker-compose)
* [Docker Networking](#docker-networking)
* [Docker Swarm](#docker-swarm)
* [Docker Volume](#docker-volume)
* [Related tools](#related-tools)
* [Kubernetes](#kubernetes)
* [FAQ](#faq)
* [References](#references)

## Docker CLI

### Docker CLI Hello World

List all images
```
docker image ls
```

Download image (download by layers)
```
docker pull ubuntu
```

Run `container` with `image` (downloads image if not already downloaded and executes it)
```
docker run ubuntu
```

*Command `docker run ubuntu` starts container and stops it right after execution, cause docker is not asked to continue running*

Run `container` with `image` and execute `ls` command inside
```
docker run -it ubuntu ls
```

Run `container` with `image` and keep it working until stop
```
docker run -it ubuntu bash
```

## Docker CLI Images

Download special version of image
```
git pull nginx:1.15.0
```

Download unofficial image
```
git pull tobi312/rpi-nginx:latest
```

Inspect image info (port, env variables, layers, entrypoint, etc.)
```
docker inspect alpine
```

Search docker images by stars from terminal
```
docker search mongo -s 500
```

Delete image
```
docker rmi mongo
```

Delete image even if it is used in container
```
docker rmi -f mongo
```

Delete all unused images
```
docker image prune
```

Show build history of image
```
docker image history my-image
```

## Docker CLI Containers

List all running containers
```
docker container ls
```

List all containers (with stopped)
```
docker container ls -a
```

Run container
```
docker run -it ubuntu bash
```

Run container with specific name (not possible to create container with the same name twice)
```
docker run -it --name ubuntu1 ubuntu bash
```

Start container with specific name
```
docker start ubuntu1
```

Stop running container
```
# c80.. is an id of running container
docker stop c80...
```

Start stopped container
```
# c80.. is an id of stopped container
docker start c80...
```

Delete stopped docker container
```
docker container rm 123abc
```

Delete any (stopped or running) docker container
```
docker container rm -f 123abc
```

Delete stopped container by name
```
docker container rm ubuntu1
```

Delete all containers (`-aq` equals to `--all --quite`)
```
docker container rm -f $(docker container ls -aq)
```

Run container and disconnect terminal-container connection (`--detach`)
```
# Local terminal will be accessible right after command is entered, it will not sleep for 15 seconds
docker run -d ubuntu sleep 15
```

Run separate process (not PID 1) within detached container
```
docker exec 123abc.. ls
```

Attach to running docker container main process (to PID 1)
```
docker attach 123abc..
```

Run container and connect local terminal to container terminal (`--interactive --tty`)
```
# When you input Ctrl+Q in bash, container stops cause it's main process (bash) is stopped
# When you input Ctrl+PQ in bash, contains is still running cause bash is not stopped
docker run -it ubuntu bash
```

Run container with specific port (`--publish <localhostPort>:<dockerPort>`)
```
docker run -p 27018:27017 mongo
```

Run container with multiple ports
```
docker run -p 27017:27017 -p 8080:8080 my-image
```

Run container and automatically delete it after stop
```
docker run -it --rm ubuntu bash
```

See docker container logs
```
docker container 123abc.. logs
```

List all docker networks
```
docker network ls
```

Create network
```
docker network create my-network
```

Run container in network
```
docker run --network my-network my-image
```

Connect container with network
```
docker network connect my-network my-container-name
```

Delete network
```
docker network rm my-network
```

List all volumes
```
docker volume ls
```

Create volume
```
docker volume create my-volume
```

Run container with volume (my-volume will be automatically created if not created before)
```
docker run -v my-volume:/dir1/bla my-image
```

Delete volume
```
docker volume rm my-volume
```

## Dockerfile Commands

TODO describe Dockerfile commands

### FROM

### MAINTAINER

### COPY

### WORKDIR

### ENV

### RUN

### ENTRYPOINT

### EXPOSE

## History

### One Server (one computer) - One App

Disadvantages:
* Price (from 20.000$)
* No technologies to run apps at one server safely
* Server can use less resources than needed
* Server can use more resources than needed
* Distribution in the world

### Virtual Machines

`VMWare`, `VirtualBox`, etc.

Advantages:
* Run apps at one server
* Resource delegation
* Move to cloud solutions
* No waste money

Disadvantages:
* VM OS-s consume resources
* Slow to boot
* Big size
* If migration comes, it should be applied for each OS
* Need license for each OS

### Linux Containers (LSX)

`Control groups` - isolating resource usage of a collection of processes.

*All Linux Family OS are build on the same kernel*

### Windows Containers (2016)
Analogue of Linux containers on Windows.

* Changes in Windows kernel
* Supported in Windows 10 and Windows Server 2016
* Windows Container
* Hyper V

### Docker

`Docker inc` product for containerization + many sub-modules (e.g. [Moby](https://github.com/moby/moby)).

## Installation

* Linux - install `Docker`
* Max - install `Docker for Mac` (it uses native ligthweight linux virtual machine `xhy.ve`) or `Docker Toolbox`
* Windows - install `Docker for Windows` (Windows 10) or `Docker Toolbox` (elder Windows)

### Docker Toolbox (old)

* Install binaries (`/usr/local/bin/docker` + `/usr/local/bin/docker-machine`)
* Add tunel configs
```
DOCKER_HOST=tcp://xxx.xxx.xxx.xxx:2376
DOCKER_MACHINE_NAME=default
DOCKER_TLS_VERIFY=1
DOCKER_CERT_PATH=$HOME/.docker/machine/default
```
* Run terminal commands

#### Docker version

Docker consists of `server` (`docker daemon`) with its REST API and `client` (CLI).
To see client and server versions, run

```
docker version
```

*Version scheme - YY-MM-xx (e.g. 18-06-45)*

## Docker Engine

Docker Engine - lightweigh and powerful open source containerization technology for applications containerizing and orcherstrating.

* Enterprise Edition
* Community Edition

See [Documentation](docs.docker.com).

It builds many open source projects to Docker CE or Docker EE.

### Docker Workflow

* Images - set of layers, your application in binary
* Containers - running instance with image
* Docker daemon - container/image manager
* Docker registry - storage of images, aka artifactory
* Docker host - machine with docker daemon (e.g. `xhy.ve` VM for Mac)

### Docker engine architecture

```
            Docker client
                  |
                  |
            Docker daemon (ask `containerd` to start new container)
                  |
                  |
              containerd (ask `runc` to create container)
                  |
      -------------------------
      |     |     |     |     |
    shim  shim  shim  shim  shim 
    runc  runc  runc  runc  runc (creates container)
```

## Docker Images

* Image is like a `stopped container`
* Image is similar to `class` (while container is similar to `object`)
* Images consists of `multiple layers`
* Image contains `OS`, `files` and `dependencies` for the app

Best practice:
* Container should be lightweight
* Images contain only essential parts (as small as possible)
* Do not contain kernel

Examples:
* Alpine Linux - recommended for applications (5MB)
* ubuntu (110MB)
* microsoft/nanoserver (over 1GB)

*There are some container-specific images. i.e. I cannot pull `microsoft/nanoserver` in linux or mac OS.*

Some images are too small and don't contain enough tools. e.g. command `docker run -it alpine bash` will not work, but `docker run -it apline sh` will work, cause it's more lightweight.

### Image registry

* Images are pulled from `image registries`
* [Docker Hub](https://hub.docker.com) is a default common registry (1 free Private Repo) with `official` and `unofficial` images.
* There are some other registries - `Google Container Registry`, `AWS ECR`, etc.

*_ in `Docker Hub` url is a mark of offical image (`https://hub.docker.com/_/nginx/`)*

### Image repository

* Image registry contains multiple Image Repositories
* Each Image Repository contains one or more images

### Image and layers

Image is an object that represents a merge of its layers

The idea is that layer can be reused, e.g. no need to redownload all the layers if some of them are already downloaded. Every layer has unique hash generated by its binary content.

```
docker inspect ubuntu
{
  ...,
  "RootFS": {
    "Type": "layers",
    "Layers": [
      "sha256:...",
      "sha256:...",
      "sha256:..."
    ]
  }
}
```

### Delete image

`docker rmi image-name`

* It is not possible to delete image if any container uses this image, cause image layers are really used in container. (`docker rmi -f image-name` will delete it)
* If a layer is shared with other images it will not be removed

## Docker Containers

Docker container is a running instance of image.

`docker run -it image-name my-command`

`-it` flags connect current terminal window to the container's shell.

Example:
```
docker run -it ubuntu bash
# b43... is a container id
root@b43..:/#
```

### Container process

* When we run container we `specify command (process) to run`
* This process (with pid 1) is called `main process`
* `Killing the main process` in the container will also `kill the container`
* `Ctrl+PQ` `exits` container `without terminating` it
* It's a good practice to have 1 process per container.

### Hardware virtualization vs OS virtualization

Hardware:
1. Physical server is powered on and hypervisor boots
2. Hypervisor divides hardware resources into virtual resources
3. Packages virtual resources into a VM

OS:
1. OS boots
2. We install container engine.
3. Container engine takes OS resources and divides them into secure isolated constructions (containers)

### Image Layers and Container Layers

```
      Thin Read/Write Layer           <- Container layer
    ↕     ↕     ↕     ↕     ↕
  91e54...                0B
  d74508..                1.895KB     <- Image layers (Read Only layer)
  c220...                 194.5KB
  d3a...                  188.1MB    
```

*AUFS () is used for file system changes inside docker containers*

### Where layers are stored?

* For Mac - `/var/lib/docker/aufs` inside `xhy.ve` VM.
* Open `Docker` -> `Preferences...` -> `Disk`.
* See [Storage Driver](https://docs.docker.com/storage/storagedriver/aufs-driver/#modifying-files-or-directories)
* Image layers and their content are stored in the `/var/lib/docker/aufs/diff` directory
* Running containers are mounted below the `/var/lib/docker/aufs/mnt` directory

It is possible to connect to docker virtual machine and see folder structure. (google `ssh to docker for mac xhy.ve`).

### Docker Ports

Docker doesn't listen to all the ports.
It listens to ports added in `EXPOSE <docker-port>` line in `Dockerfile`.
Use `docker run -p <localhost-port>:<docker-port> -p <another-localhost-port>:<another-docker-port>`.

### Fun

There is a dockercraft container (`Docker` + `Minecraft`). And many other funny containers :smile:

### Restart Policy

Restart Policy - enables Docker to automatically restart them after certain events or failures have occured
* always - always restart after failure/exit/stop
* unless-stopped - restart after failure/exit (not after `docker stop`)
* on-failed - restart after failure (e.g. error code)

`docker run --restart <policy>`

*ps aux will always return PID 1 inside running docker container*

`docker run -it --restart always ubuntu sleep 5`

## Dockerfile

* Describes how to pack the application into image
* Can be used as a documentation

* Usually Dockerfile is located in the root of the project

### Spring Boot example

1. Build executable file
```
cd my-example
./gradlew build
```

2. Add Dockerfile
```
# FROM is required for all Dockerfiles, it means that your image is based on image in FROM section
FROM openjdk:8-jdk-alpine

# Any author name :-)
MAINTAINER lehaSVV2009@gmail.com

# Copy executable file to the image root
COPY build/libs/my-example-0.0.1-SNAPSHOT.jar /app/app.jar

# WORKDIR sets all commands below to use /app relative path (e.g. cd app)
WORKDIR /app

# RUN Usually used for preinstallations
RUN echo HELLO

# Docker will listen to 8080
EXPOSE 8080

# PID 1 is based on ENTRYPOINT
ENTRYPOINT ["java", "-Djava.security.edg=file:/dev/./urandom", "-jar", "app.jar"]
```

* Use your own `WORKDIR` cause if external image has non-root `WORKDIR`, there might be some troubles with access to your app
* It is possible to add "/app/app.jar" to ENTRYPOINT without WORKDIR

3. Build docker image
```
docker build -t my-example .
```

* For building Dockerfile above 3 docker containers were used (for `FROM` and `COPY`, for `RUN` and for `ENTRYPOINT`)
* It's a good practice to build images in the same machine (cause layers are cached and it will be faster)
* The image will contain `4 layers` - 1 for current image and 3 for `openjdk:8-jdk-alpine`
* If you add `RUN echo BLA >> file.text`, it will create one more layer.

4. Run docker container
```
docker run -it -p 8080:8080 --name my-example-container my-example
```

5. Publish to Docker Hub
```
docker login
docker image tag my-example:latest my-docker-account/my-example:latest
docker push my-docker-account/my-example:latest
```
*If the image is not official, you should add your account name before image*

## Docker Compose

Docker compose - tool to deploy and manage multi-container apps in single-engine mode.

* Written on `Python`
* Acquired by `Docker inc` in `2014`
* Lets to describe an entire application in simple `YML`

See [Docker compose versions](https://docs.docker.com/compose/compose-file/compose-versioning/) to be sure that your `docker-compose` version matches your `docker` version. 

### Docker Compose Spring Boot Example

`Dockerfile`
```
FROM openjdk:8-jdk-alpine
COPY build/libs/my-example-0.0.1-SNAPSHOT.jar /app/app.jar
WORKDIR /app
EXPOSE 8080
ENTRYPOINT ["java", "-Djava.security.edg=file:/dev/./urandom", "-jar", "app.jar"]
```

`docker-compose.yml`
```
version: "3.6"

services:
  my-example:
    build: .
    ports:
      - 8080:8080
```

### Docker Compose commands

Run all containers described in docker-compose
If any changes are done in image it recreates containers
```
docker-compose up
```

List current docker-compose containers logs
```
docker-compose logs
```

Delete all running docker containers
```
docker-compose down
```

Run 3 containers of service listening same port
```
docker-compose scale my-service=3
```

## Docker Networking

Let's imagine that service 1 (exposed in 8080) calls `http://localhost:8081` of service 2 (exposed in 8081).

If you run it in the way below, call from service 1 to `http://localhost:8081` of service 2 will fail cause service 1 will try to go to container localhost of service 1, but service 2 has another container localhost.

```
docker run -it --name second-service second-image
docker run -it --name first-service first-image
```

Possible solutions:

1. Connect by IP (bad)

```
docker run --name second-service -it second-image

docker inspect second-service
{ 
  ...,
  "Networking": {
    ...,
    "IPAddress": "172.17.0.5"
  }
}

docker run -it --name first-service -e SECOND_SERVICE_API=172.17.0.5 -p 8080:8080 first-image
```

2. Option 2. Create network

```
docker network create my-net
docker run -it --network my-net --name second-service second-image
docker run -it --network my-net --name first-service -e SECOND_SERVICE_API=second-service first-image
```

3. Option 3. Create network in docker-compose

`docker-compose`
```
version: "3.6"

services:

  first-service:
    image: my-dockerhub-account/first-image
    ports:
      - 8080:8080
      - 8081:8081
    environment:
      - SECOND_SERVICE_API=second-service
    networks:
      - my-net

  second-service:
    image: my-dockerhub-account/second-image
    networks:
      - my-net

  networks:
    my-net
```

`Execute`
```
docker-compose up -d
```

### Network Drivers

* `bridge` (default)
* `overlay` - 
* `macvlan`

## Docker Swarm

A swarm is a group of machines that run Docker and are joined into a cluster.

### Docker Machine

The tool for application provisioning (e.g. clustering).

* `docker-machine ls` - list all docker machines
* `docker-machine create my-manager-1` - create new docker machine. (Creates VM with docker inside, "virtualbox" by default).
* `docker-machnie create --driver amazonec2 --amazonec2-open-port 8000 --amazonec2-region ... my-aws-machine-name` - create new VM with docker for AWS.
* `eval $(docker-machine env my-manager-1)` - tunneling (on you terminal you work as on `my-manager1` machine)
* `docker-machine active` - info about current docker-machine (useful if tunneling is used)
* `docker-machine kill` - delete docker machine

### Docker Sock

Socket for docker
* Client talks to the daemon via a local IPC/Unix socket at `/var/run/docker.sock` (might be mounted as a volume).
* Via named pipe at `npipe:////./ pipe/ docker_engine` (Windows)

### Portainer

Image `portainer` is often used for examples. It is nice Admin for docker management. See [docs](https://portainer.io).

It is possible to connect to remote docker machine by use docker sock as a volume:
```
docker volume create pontainer_data
docker run -d -p 9000:9000 --name portainer -v  /var/run/docker.sock:/var/run/docker.sock pontainer_data:/var/bla  portainer/portainer
```

### Swarm Overview

* `Swarm` consists of one or more Docker nodes (physical servers/VM-s/cloud services).
* The only requrement is that all nodes can communicate over reliable networks.

### Swarm Nodes

* Nodes: `managers` and `workers`
* Managers look after the control plane of the cluster
* Workers accept tasks from managers and executer them
* Managers also operate as workers

```
      Internal distributed data store
        |             |             |
      Manager       Manager___    Manager___   
   _____|        _____|  _____|______|      |
  |     |       |       |     |      |      |
Worker Worker Worker Worker Worker Worker Woerk
```

[Raft Consensus Algorithm](https://raft.github.io/) - algorithm of orchestration used in Docker Swarm

*There should be odd number of nodes*

### How to create cluster

Init docker swarm with manager
```
$ docker-machine active
my-manager-1
$ docker-machine ls
... tcp://192.168.99.100:..
$ docker swarm init --advertie-addr 192.168.99.100
Swarm initialized
$ docker node ls
ID HOSTNAME STATUS AVAILABILITY *(leader)
```

Connect worker to cluster
```
$ docker-machine create my-worker-1
$ eval $(docker-machine env my-worker-1)
$ docker-machine ssh my-manager-1 "docker swarm join-token worker"
To add a worker to swarm
docker swarm join --token SW... 192.168.99.100:2377
$ docker swarm join --token SW... 192.168.99.100:2377
This node joined a swarm as a worker
$ docker-machine ssh my-manager-1 "docker node ls"
```

## Docker Volume

### Contianer Writable layer

* Every container gets its own writable non-persistent storage (writable layer)
* It is automatically created, alongside the container, and it is tied to the lifecycle of the container
* It should not be used to persist data

### How to persist data?

* Create volume
* Mount volume to container

```
docker volume create vol1
docker run -v vol1:/dir1/bla
```
OR
```
docker volume create vol1
docker run --mount source=vol1,target=/dir1/bla
```
OR SIMPLY
```
docker run -v vol1:/dir1/bla
```
* By default it creates a volume with local driver (available only in the node where volume is created)
* If you specify an existing volume, docker will use the existing one
* If you specify not existing volume, docker will create new one
* It is possible to mount shared data

## Related tools

* [Kubernetes](https://kubernetes.io/)
* [Amazon ECS](https://aws.amazon.com/ecs/)
* [Openshift](https://www.openshift.com/)
* [rkt](https://github.com/rkt/rkt)
* [Mesos](http://mesos.apache.org/)
* [CoreOS](https://coreos.com/)

## Kubernetes

Container orchestration framework
* container agnostic
* initially was developed by Google
* donated by Cloud Native Computing Foundation

Consists of pods, deployments, services, deployment configs, routes, etc. Quite flexible.

## FAQ
**What should I do if my build version is changed?**

You can use `gradle docker plugin`, it support docker app versioning.
OR you can use `Dockerfile.tpl` and generate Dockerfile with valid version while building.

**What should I do if I want `cd bla && mkdir xx && cd ..` in ENTRYPOINT?**

You should use `RUN` before `ENTRYPOINT` for preinstallations.

## References

* [Docker Documentation](https://docs.docker.com/) - docker docs
* [Docker Hub](https://hub.docker.com/) - open and free docker image registry
* [Play with docker](https://play-with-docker.com) - online service for playing with docker by terminal
* [Docker in AWS](https://docs.docker.com/docker-for-aws/)
* [Cloud Native Computing Foundation](https://www.cncf.io/projects/)