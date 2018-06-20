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
*Allocate a pseudo-tty - `-t`, Keep STDIN open even if not attached - `-i`*

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

Inspect image info
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

# Any name :-)
MAINTAINER lehaSVV2009@gmail.com

# Copy executable file to the image root
COPY build/libs/my-example-0.0.1-SNAPSHOT.jar /app/app.jar

# WORKDIR sets all commands below to use /app relative path (e.g. cd app)
WORKDIR /app

# RUN Usually used for preinstallations
RUN echo HELLO

# Docker will listen to 
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

4. Push to Docker Hub
```
docker login
docker image tag my-example:latest my-docker-account/my-example:latest
docker push my-docker-account/my-example:latest
```
*If the image is not official, you should add your account name before image*

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
