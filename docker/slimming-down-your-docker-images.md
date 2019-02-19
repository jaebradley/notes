# [Slimming Down Your Docker Images](https://towardsdatascience.com/slimming-down-your-docker-images-275f0ca9337e)

* When building a Docker image, Docker looks through Dockerfile line by line
* Docker looks for each instruction's intermediate image in it's caching layer
* If it cannot find this instruction's image in it's caching layer, it, and all subsequent images are regenerated
* So if base image is not cached or is a cache miss, the entire image is regenerated
* So Docker will look for the `RUN pip install -r requirements.txt` instruction in locally cached intermediate images
  * No comparison between old and new requirements file is done (so no way to know if `requirements.txt` has been updated...yet)
* `ADD` and `COPY` do require Docker to take a look at file contents to see if anything has changed - if file is different than cache is invalidated
* If instructions are often going to change in Dockerfile, to take advantage of caching put these instructions as late in the Dockerfile as possible
  * This way, only a smaller subset of the Dockerfile will be regenerated when those instructions change
* To properly invalidate earlier `requirements.txt` example,
  * `COPY` the file first (since the file has changed, this instruction / cached image will be out of date and will force all subsequent images / caches to be regenerated)
* Use Alpine images as they're usually the best-maintained / slimmest / simplest
* Multistage builds use multiple `FROM` instructions
  * Can copy files from one stage to another and can leave behind anything you don't want in final image
  * Each `FROM` begins new stage of build
  * Leaves behind any state from previous stage
  * Can use different base
  * Adds complexity and is used to optimize memory as much as possible
  * Alias prior stage's build like `FROM golang:1.7.3 AS build` so it can be used later like `COPY --from=build`
* `.dockerignore` can help cache control simply by limiting any unnecessary files (both reducing image size and unnecessary files updating like log files and invalidating cached images)
* `docker container ls -s` - approximate size of running container
* `docker image ls` - size of images
* `docker image history my_image:my_tag` - size of intermediate images used to make up image
* Combine `RUN` `apt*` commands, including package installations to keep layer count down
  * `rm -rf /var/lib/apt/lists/*` at end of aforementioned `RUN` command so that `apt` cache is not stored in layer
