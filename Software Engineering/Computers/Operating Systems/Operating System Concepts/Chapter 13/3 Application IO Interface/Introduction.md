# 13.3 Application I/O Interface

* The purpose of the device-driver layer is to hide the differences among device controllers from the I/O subsystem of the kernel
* Hardware manufacturers either design new devices to be compatible with an existing host controller interface, or write device drivers to interface the new hardware to popular operating systems
* Each operating system has its own standards for device drivers
* Major access conventions are
  * Block I/O
  * Character-stream I/O
  * Memory-mapped file access
  * Network sockets
* Backdoor for passing arbitrary commands from an application to a device driver
  * `ioctl` in UNIX
  * `ioctl` system call enables an application to access any functionality that can be implemented by any device driver
