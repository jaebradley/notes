# [Pump Up The Volumes: Data In Docker](https://towardsdatascience.com/pump-up-the-volumes-data-in-docker-a21950a8cd8)

* Files created by an application inside a container are stored in the writable layer of the container
  * `tmpfs` mount has faster read/write operations
* When container ceases to exist, data will disappear too

## Volumes

* Files system that lives on a host machine outside of any container
* They are
  * shareable with other containers
  * able to be hosted on remote cloud providers
  * encrytable
  * nameable
  * content pre-populated by containers
* Dangling volumes are volumes not used by a container
* `docker volume ls` - list docker volumes
* `docker volume inspect some_volume` - inspect specific volume
* `docker volume prune` - removes dangling volumes
  * Cannot remove volumes until all associated containers are deleted
* `--mount` options
  * `type` (`bind`, `volume`, `tmpfs`)
  * `source` - name of volume
  * `destination` - the path where the file / directory is mounted in the container
