# docker-training

Containerization - technology for providing multiple isolated environments on a single host

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

## Docker Engine

Docker Engine - lightweigh and powerful open source containerization technology for applications containerizing and orcherstrating.

* Enterprise Edition
* Community Edition

See [Documentation](docs.docker.com).

It builds many open source projects to Docker CE or Docker EE.

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

## Docker version

Docker consists of `server` (`docker daemon`) with its REST API and `client` (CLI).
To see client and server versions, run

```
docker version
```

*Version scheme - YY-MM-xx (e.g. 18-06-45)*

## Docker Workflow

* Images - set of layers, your application in binary
* Containers - running instance with image
* Docker daemon - container/image manager
* Docker registry - storage of images
* Docker host - machine with docker daemon (e.g. `xhy.ve` VM for Mac)

## Hello World

List all images
```
docker images ls
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

List all running containers
```
docker container ls
```

List all containers (with stopped)
```
docker container ls -a
```

## Docker engine architecture

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