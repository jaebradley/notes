# Docker Images

* An image is made up of filesystems that are layered on top of each other
* Root filesystem is read-only
* Docker uses `union mount` to add more read-only filesystems onto root filesystem
  * `union mount` is a mount that allows several filesystems to be mounted at one time but appear as one filesystem
  * Resulting filesystem can contain all files and subdirectories of underlying filesystem
* Each mounted filesystem is called an image
* Images can be layered on top of one another from the parent image at the top to the base image at the bottom
* Container is launched using an image it uses a read-write filesystem on top of all the read-only image layers
  * This filesystem is where the processes we want our container to run will be executed
  * On container start, the writeable layer is empty
  * As changes occur (like changing a file) the change is copied from the read-only layer into the read-write layer
    * Read-only version of the file still exists but is "hidden underneath" the copy
    * This is called "copy on write"

```
writeable container (read-write)
add apache <-- next image (read-only)
add emacs <-- next image (read-only)
ubuntu <-- base image (read-only)
bootfs (read-only)
```

* Local images live on local docker host in `/var/lib/docker` directory
  * Each image will be inside directory named for storage driver (`aufs` or `devicemapper`)
* Local containers live at `/var/lib/docker/containers` directory
