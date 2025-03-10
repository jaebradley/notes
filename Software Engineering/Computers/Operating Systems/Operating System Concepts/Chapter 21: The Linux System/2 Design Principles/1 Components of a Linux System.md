# 21.2.1 Components of a Linux System

* Kernel - maintains all important abstractions of the operating system (like virtual memory, processes)
* System libraries - Standard set of functions through which application interact with the kernel
* System utilities - Programs that perform individualized, specialized management tasks
  * Some system utilities only need to be invoked and initialized once
  * Others (daemons) may run permanently
* All kernel code executes in the processor's privileged mode with full access to all the physical resources of the computer (kernel mode)
  * All operating-system-support-code that does not need to run in kernel mode is put into system libraries
* All kernel code and data structures are kept in the same address space
  * Including device drivers, file systems, networking code
* Linux kernel can load and unload modules dynamically

## System Libraries

* Applications make calls to the system libraries, which in turn call the operating-system services (the kernel)
* Making a system call involves transferring control from unpriviledged user mode to privileged kernel mode
* Libraries may provide more complex versions of the basic system calls
  * The C language's buffered file-handling functions are all implemented in the system libraries
  * They provide more advanced control of the I/O than basic kernel system calls
