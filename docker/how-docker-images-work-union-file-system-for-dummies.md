# [How Docker Images Work: Union File System For Dummies](https://www.terriblecode.com/blog/how-docker-images-work-union-file-systems-for-dummies/)

* Unionfs is a filesystem service for Linux that implements a union mount for other file systems
  * It allows files and directories of separate file systems (branches) to be transparently overlaid, to form a single coherent file system
  * Directories which have the same path within the merged branches will be seen together in a single merged directory within the new virtual filesystem
* `overlay(fs)` works similarly to overhead project sheets (base "image" and then images that are "layered" on top
* Base layer
  * Base files for file system (base image)
  * Read only
* Overlay layer
  * Writes to this layer and stored in diff layer
  * Offers union view of Base and Diff layers (Diff layer files supersede Base layer's)
  * Oversimplification but this is the layer you see whenever you run a container
* Diff layer
  * Any changes in Overlay layer are stored in this layer
  * If changes are made to something that exists in base layer then `overlay(fs)` will copy the file to the diff layer and then make modifications you attempted to write
    * This is called `copy-on-write` and is critical to Union File System
* Can replicate this by creating "layers" through different directories and then using the `mount` command
* This is effectively how the Docker `overlay2` storage driver is implemented
