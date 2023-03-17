# Docker Compose

* `docker-compose.yml` file contains instructions for running multiple containers with various runtime configurations
* `version` key indicates Docker Compose API to use
* Each service must be specified under the YAML `services` hash
* For each service definition, the specific image and (probably) tag should be specified
* Can also specify the port mapping between the service and the host
* Can also specify any volumes to create
* `-d` option to daemonize / run in the background
* compose attaches logs that have service identifiers
* `docker-compose up`

