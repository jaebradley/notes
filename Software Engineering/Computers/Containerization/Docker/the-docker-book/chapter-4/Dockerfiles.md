# Dockerfiles

* The directory that contains a `Dockerfile` is considered the context or build context
* Docker will upload the build context (including all files and directories) to the Docker daemon when the build is run
* Each instruction in the `Dockerfile` adds a new layer to the image and commits the image
  * Since every layer is committed Docker can cache previous built images as a cache when rebuilding an image
  * So if you have an `ENV` instruction (which sets an environment variable in the image) that's set to a given value and then it's value is changed, all instructions after it will be run anew
* The first instruction in a `Dockerfile` must be `FROM`
  * `FROM` specifies an existing image that the following instructions will operate on i.e. the base image
* `RUN` instruction executes in a shell using command wrapper (`/bin/sh -c`)
  * If shell is unavailable can also use `exec` format like `RUN ["first", "second", "third"]`
* `EXPOSE` instruction tells Docker that container will use a specific port
  * Docker won't open the port automatically - you need to do this using `docker run`
* Docker will automatically tag images as `latest` if a tag is not specified

## Launching a Container

* Specifying a port via the `-p` flag
  * Docker randomly assigns a high port (32768 to 61000) on host that maps to specified port on container
  * Can specify specific port on Docker host that maps to the specified port on the container
    * Be careful with this as only one container can bind to specific port on the local host

## Other Dockerfile instructions

* `CMD`
  * Runs command on container start
  * Constrasted with `RUN` which eecutes on container launch
  * If command is not in an array, Docker will prepend `/bin/sh -c` to it - generally use array syntax
  * If a command is specified via `docker run` then the `docker run` command will override the `CMD` instruction
  * Can only specify one `CMD` instruction per `Dockerfile` - docker will use last `CMD` instruction defined
* `ENTRYPOINT`
  * Like `CMD` except arguments in `docker run` are passed to command in `ENTRYPOINT`
  * Can combine `ENTRYPOINT` and `CMD` such that `CMD` passes `ENTRYPOINT` a flag if it's not overridden by `docker run`
* `VOLUME`
  * Volume is a special directory in one or more containers that bypasses Union File System
  * Volumes can be shared and reused between containers
  * Changes to a volume are made directly and are not included when you update an image
  * Volumes persist even if no containers use them
  * Volumes allow the ability to add a database or other content into an image without committing it to the image
  * Volumes allow the ability to share data between containers
* `ADD`
  * Adds files and directories from build environment to image
    * Cannot add files from outside build context
    * Docker will use last character (i.e. `/` to determine if the source is directory or file`
  * Docker will also automatically unpack `tar` archives
* `COPY`
  * Does not have any extraction or decompression capabilities
  * Can only copy within build context
  * If destination doesn't exist, it is created (like `mkdir -p`)
* `ARG`
  * Variables that can be passed at build-time via `docker build`
  * Don't pass secrets or keys like this as credentials will be in build history of image

