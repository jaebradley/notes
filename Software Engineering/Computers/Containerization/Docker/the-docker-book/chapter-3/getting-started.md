# Chapter 3

* `docker info` command outputs information about containers, images, execution / storage drivers that Docker uses and any other basic config
* Basic structure of Docker is client/server architecture
  * One binary is docker server via `dockerd` binary
  * Other binary is docker client via `docker` binary which passes requests to Docker daemon and then processes responses
* `docker run -i -t ubuntu /bin/bash`
  * `-i` flag keeps `STDIN` open even if not attached to it
  * `-t` tells Docker to assign a terminal to container
  * Combined they provide an interactive shell
  * `ubuntu` part of command indicates that we wanted to create container from `ubuntu` image
    * `ubuntu` base image is provided by Docker via Docker Hub registry
  * If Docker couldn't find `ubuntu` image on local Docker host so it reached out to Docker Hub registry
  * After downloading image it stored it on local host
  * Docker uses image to create a new container inside a filsystem
  * Container has a network, IP address, and ability to talk to local host
  * `/bin/bash` defines the command to run in new container
* Can also checkout `/etc/hosts` file to see various IP address mappings
  * First field is IP address and then each additional word is a host name 
  * Configuration just maps IP addresses to defined hostname
