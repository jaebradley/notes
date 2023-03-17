# [Running a Docker container as a non-root user](https://medium.com/redbubble/running-a-docker-container-as-a-non-root-user-7d2e00f8ee15)

* When building Docker containers, the build creates files in a host folder (like the source code directory) that's then mounted into the cotnainer
  * Those files are owned by the `root` user so when a user tries to clean up those files they might not have the permissions to remove those files (for example)
* Could tell Git (or any other dependents) to ignore affected files but files might just hang around file system and bloat memory consumption
* Clean up files at end of build, but requires a lot of error-handling logic to ensure cleanup always happens while also exiting the buld correctly
* Can use a user ID and a group ID when running a container with Docker
  * Docker containers share the same kernel and thus the same list of UIDs and GIDs

```bash
docker container run --rm -it \ # Remove volumes when container is removed and the ability to attach / detach to the container
  -v $(app):/app \ # Mount source code
  --workdir /app \ # set working directory
  --user 1000:1000 \ # run as specified user
  --docker/app-build:latest \ # build image
  some_command_to_execute
```

* Any files created by command will belong to user 1000
* Can dynamically use current user's id by doing something like `--user $(id -u):$(id -g)`
* In `docker-compose` can't use subshells so use `CURRENT_UID` environment variable where it's value is `$(id -u):$(id -g)`
* Docker container user will not have a `$HOME`
* Docker container user won't have a name - `whoami` inside container will return `I have no name!`
