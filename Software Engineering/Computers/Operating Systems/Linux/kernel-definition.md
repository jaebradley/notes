# [Kernel Definition](http://www.linfo.org/kernel.html)

* The kernel is a program that constitutes the central core of a computer operating system
* The kernel is the first part of the operating system to load into memory during booting / system start up and it remains there for the duration of the computer session because its services are required continuously
* The kernel code is usually loaded into a protected area of memory, which prevents it from being overwritten by other programs
* When a computer crashes, it actually means the kernel has crashed
* The kernel provides basic services for all other parts of the operating system, typically including memory management, process management, file management and I/O management (like accessing peripheral devices)
  * These services are requested by other parts of the operating system or by application programs through a specified set of program interfaces referred to as system calls
* Kernel generally consists of
  * A scheduler, which determines how the various processes share the kernel's processing time (and in what order tasks should be executed in)
  * A supervisor, which grants use of the computer to each process when it is scheduled
  * An interrupt handler, which handles all requests from the various hardware devices, like disk drives and keyboard
  * Memory manager, which allocates the system's address space
