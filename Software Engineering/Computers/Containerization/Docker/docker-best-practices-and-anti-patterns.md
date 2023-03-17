# [Docker Best Practices And Anti-Patterns](https://medium.com/better-programming/docker-best-practices-and-anti-patterns-e7cbccba4f19)

* Tags are aliases for docker image id (like Git tags which refer to particular commit) allowing versions for Docker images at different points in time
  * Forgetting to tag has one major drawback - the default tag is `latest` but if recreating docker image from a `Dockerfile` the "latest" image might not actually be the latest
  * Using `python3:latest` is not great because each pull might build an entirely different Docker image than previous build - pin specific tags for image like `python3:1.0.1` so that dependencies are immutable
* Take advantage of `LABEL` to add relevant metadata to the image
  * For example, setting an `Author` field so it's easy to know who to contact
* By default, Docker containers run as `root`
  * Docker container running as `root` has full control of host system
  * Docker images should be run as non-`root`
  * Might have to do some privileged tasks as `root` before switching to non`-root` user
* Don't `COPY` sensitive information in a Dockerfile
  * Store sensitive data on a secure filesystem that container connects to like AWS Elastic Block Storage or S3
  * Avoid storing security credentials in Docker - use environment variables using `e`-arguments or use `env-file` to read from environment variables
* Don't store data or logs inside container - when container shuts down, the data / logs are lost
  * Docker installation as three levels of logging - the container, the docker service, and the host operating system
* Don't write to container filesystem - it activates copy-on-write strategy which can put load on storage drivers
  * So write data to volumes instead
* Use a tool like `hadolint` to do Dockerfile linting