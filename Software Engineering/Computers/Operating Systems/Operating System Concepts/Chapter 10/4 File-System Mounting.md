# 10.4 File-System Mounting

* A file-system must be mounted before it can be available to processes on the system
* Operating system is given the name of the device to mount and a mount point
  * The mount point is the location in the file structure where the file system is to be attached
* Operating system verifies that the device contains a valid file system
  * OS asks the device driver to read the device directory and verify the irectory has the expected format
* OS notes the file system mounted at specific point in its directory structure
  * Enables OS to traverse its directory structure, switching among file systems

## Mac OS

* When the OS encounters a disk for the first time, the OS searches for a file system on the device
* If a file system is found, it automatically mounts the file system at the root level
* All file systems are mounted under the `/Volumes` directory
* The Mac OS X GUI hides this fact and shows the file systems as if they were all mounted at the root level
