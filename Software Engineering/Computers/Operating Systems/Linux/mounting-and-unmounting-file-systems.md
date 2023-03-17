# [Mounting and Unmounting File Systems](https://docs.oracle.com/cd/E19455-01/805-7228/6j6q7ueup/index.html)

* Before you can access the files on a file system, it needs to be mounted
* Mounting a file system attaches that file system to a directory (mount point) and makes it available to the system
  * The root file system is always mounted
  * Any other file system can be connected or disconnected from the root file system
* When you mount a file system, any files or directories in the underlying mount point directory are unavailable as long as the file system is mounted
* These files are not permanently affected by the mounting process and they become available again when the file system is unmounted
* Let's say there's a file system in the `/opt` file system that contains a set of directories you want to access
  * First create a directory called `opt/whatever` to use as the mount point for the file system you want to mount
  * Once the mount point is created, you can mount the file system which makes all of the files and directories in the mounted file system available
* It would be very time-consuming to manually mount file systems every time you wanted to access them
  * To fix this, the virtual file system table was created to maintain a list of file systems and how to mount them
  * Can mount file systems by using only the mount point name, because the `/etc/vfstab` file contains the mapping between the mount point and the actual device slice name
