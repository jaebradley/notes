# [Understanding Docker as if it were a Game Boy](https://medium.com/@audretschjames/understanding-docker-as-if-it-were-a-gameboy-96c96392efbf)

* Use docker because software installation is hard and don't have to worry about user's host system - if can use docker, it should work in every environment
* Docker image is like a GB cartridge
  * Standardized to run on any GB
* Container is a running instance of an image
  * Like running a game in a GameCube
  * Container is isolated from host system like an isolated virtual machine
  * Like when playing Mario Kart, now matter what happens in the game, it (shouldn't) affect the Gamecube itself. Nor should it impact the game disk either.
* Dockerfile is instructions to ensure a particular execution environment
  * The fact that the instructions are dictated in a file means that the instructions are shareable / reusable
* Mounting in a docker container is when a host machine directory is readable and writable inside the container
  * `docker run -v <HOST_DIRECTORY>:<CONTAINER_DIRECTORY>`
* Docker volumes are like memory cards - they're portable, still exist / saves data when GameCube is turned off, and can use many different memory cards which hold different data
  * Can attach a data volume to any container and that container can access the memory stored in that volume
* When networking in Docker, ensure to expose the container port, and then map the container port to the host
  * Analogy is hooking PS4 to TV via HDMI port on PS4 and HDMI port on TV
  * HDMI port on PS4 is "exposing" container port
  * Mapping container port to host is the HDMI cable from PS4 to TV
  * Specify container port in `Dockerfile` using `EXPOSE <PORT_NUMBER>` instruction
  * Specify mapping by doing something like `docker run -p <HOST_PORT>:<CONTAINER_PORT>` when running container
* Each command / layer in `Dockerfile` can be thought of as a checkpoint
* Docker caches from last place in `Dockerfile` that was built
  * If adding a new command, it will only cache image state from _before_ command
* Docker compose is container orchestration
  * Part of the benefit is taking the CLI args passed when running containers and consolidating them in a YAML file instead
  * Can specify multiple network mappings in `docker-compose.yml` - while containers may have same port inside the container, the external port must be different
  * Docker creates aliases for IP addresses of linked containers automatically - the aliases are the service names defined in `docker-compose`
* Containers are supposed to be ephemeral - they should be able to be spun up and deleted at any time
  * For data that needs to persist across containers, define a named docker volume that will store this data
