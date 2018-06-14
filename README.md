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
* Docker registry - storage of images, aka artifactory
* Docker host - machine with docker daemon (e.g. `xhy.ve` VM for Mac)

## Docker CLI

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
*Allocate a pseudo-tty - `-t`, Keep STDIN open even if not attached - `-t`*

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

Start stopped container
```
# c80.. is an id of stopped container
docker start c80...
```

Stop running container
```
# c80.. is an id of running container
docker stop c80...
```

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

Run container with specific name (not possible to create container with the same name twice)
```
docker run -it --name ubuntu1 ubuntu bash
```

Start container with specific name
```
docker start ubuntu1
```

Delete stopped docker container
```
docker container rm 123abc
```

Delete any (stopped or running) docker container
```
docker container rm -f 123abc
```

Delete all containers
```
docker container rm -f $(docker container ls -aq)
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
* `Google Container Registry`, `AWS Registry`, etc.

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

## Docker containers

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