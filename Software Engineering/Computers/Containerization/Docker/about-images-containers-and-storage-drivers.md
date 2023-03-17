# [About Images Containers and Storage Drivers](https://docs.docker.com/v17.09/engine/userguide/storagedriver/imagesandcontainers/)

## Images

* An image is composed of layers
  * Each layer represents an instruction in the image's Dockerfile
  * Each layer, except the last layer is read-only

```bash
FROM ubuntu:15.04
COPY . /app
RUN make /app
CMD python /app/app.py
```

* Each command in example Dockerfile creates a layer
  * `FROM ubuntu:15.04` creates a layer from Ubuntu image
  * `COPY . /app` adds files from Docker client's current directory
  * `RUN` builds application
  * `CMD` layer specifies what command to run in container
* Each layer is set of differences from layer before it
* When container is created, a writable (container) layer is added on top of prior layers
* All file modifications are written to this container layer
* Here's what a container looks like

  1. Container / Writable Layer
  2. Image Layers (Read-Only)
     1. Image Layer 1
     2. Image Layer 2
     3. Image Layer 3
     4. Image Layer 4

* A storage driver handles how these layers interact with each other

## Containers

* When a container is deleted, it's writable layer is also deleted, but **the underlying image is unchanged**
* Multiple containers can share access to the same underlying images but have different data state
  * **If multiple images need access to the same exact data, store data in Docker volume and mount it into container**
* Size
  * `docker ps -s`
    * `size` column is the amount of disk space that the container's writable layer is using
    * `virtual size` is the amount of data that the underlying read-only images are using
      * Because images and layers can be shared between containers, simply summing the `virtual size` column will over-estimate disk usage
* Copy-on-write
  * If a container file is modified, that file is copied from the layer where it exists and is copied (and modified) in the container's writable layer
  * Files are searched one layer at a time (from newest to base) and first matching file is returned
    * File is also added to cache
  * **Containers that write a lot of data will consume more space than containers that don't**
  * **Use Docker volumes for write-heavy applications**
    * Built for I/O and do not increase size of container's writable layer
* Storing images
  * When images are pulled down using `docker pull` or when a container is created for an image that does not exist locally, each layer is created and stored in Docker's local storage area (`/var/lib/docker`)
  * Each layer is stored in own directory in `/var/lib/<storage-driver>/layers`
    * Directory names **do not** correspond to layer names

## Volumes

* Volumes are directories or a file from the host's file system that gets mounted inside a Docker container
* Volumes are not accessed using the storage driver
  * Volumes exist outside the local storage area
  * Containers exist in Docker's local storage area (`/var/lib/docker/container1`)
  * Volumes exist in some separate host directory like `/data/` or something
* Multiple containers can use the same data volume
