# Introduction to Working with Containers

## See containers

* `docker ps -a` shows existing containers
  * `-a` flag shows all containers
  * Only running containers by default
  * Add `-x` flag to show last `x` containers
  * Add `-q` flag to return container IDs
 
## Container naming

* Docker will automatically generate container names randomly
* To specify a specific container name, use `--name` flag in `docker run`
* Docker names are unique - cannot create two containers with the same name

## Starting and Attaching to a Container

* Can start containers using name or container id - `docker start {name | containerId}`
* After starting container, then can attach to container (so join previous interactive session in running container)

## Creating Daemonized Containers

* Used for running longer running containers (like for applications
* Daemonized containers don't have interactive sessions
* Create a daemonized container that while loops like
  * `docker run --name {container_name} -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"`

## Inspect Container Logs

* `docker logs {container_name}`
  * Use `-f` flag to tail logs
  * Can get last `X` lines by doing `--tail {some number}` flag
  * Can follow logs from last lines without reading log file via `docker logs --tail 0 -f {my_container_name}`
  * `-t` flag adds timestamp

## Inspect Container Processes

* `docker top {container_name}` to see processes running inside container

## Inspect Container Statistics

* `docker stats {container_1} {container_2}`
* Shows stats for one or more running docker containers
* Shows CPU, memory, network, storage I/O

## Running a process inside running container

* Background tasks run inside container without any type of interaction (like an interactive terminal)
* Interactive tasks are useful for tasks like opening a shell inside a container
* `docker exec -d {container_name} {some command}`
  * The `-d` flag indicates it's a background process
* `docker exec -t -i {container_name} {some command}
  * `-t` and `-i` flags like in `docker run` open a terminal and capture `STDIN`
  * The specified command (like `/bin/bash`) will create a new `bash` session inside the container

## Stopping a daemonized container

* `docker stop {container_name}`
* `stop` will send a `SIGTERM` signal to container's running process
* `docker kill` will send a `SIGKILL` instead
  * `SIGKILL` can't be ignored by running processes while `SIGTERM` can be

## Automatic container restarts

* `docker run` also takes a `--restart` flag that will potentially automatically start the container based on the exit code
* `always` will obviously _always_ restart
* `on-failure` will restart the container if it exits with a non-zero exit code
* `on-failure` will also take an optional restart count like `--restart=on-failure:5`

## Inspectiving Coontainer

* `docker inspect` shows configuration information
* `--format` flag that takes something like `'{{ .State.Running }}'` where you can query returned object

## Delete All Containers

* `docker rm -f 'docker ps -a -q'`
* The `-a` flag lists all containers and the `-q` flag returns container IDs
* `-f` flag will remove any running containers
