# 13.5 Transforming I/O Requests to Hardware Operations

* UNIX represents device names in the regular file-system name space
* UNIX has a mount table that associates prefixes of path names with specific device names
* In UNIX, lookup of the path name in the mount table to find the longest matching prefix
  * Entry has the device name
* Device name does not reference an inode number but a major and minor device number
  * Major device number is the device driver that should be called to handle I/O to the device
  * Minor device number is used to identify the port or memory-mapped address of the device controller

## Life cycle of a blocking read request

1. `read` system call for a previously opened file (identified using a file descriptor)
1. If data is already available in the buffer cache, the data is returned to the process and the I/O request is completed
1. If data is not in the buffer cache, physical I/O must be performed
  1. Process is removed from the run queue and is placed in the wait queue for the device
  1. I/O subsystem sends request to the device driver
1. Device driver allocates kernel buffer space to receive the data and schedule the I/O
1. Driver sends messages to the device controller by writing into the device-control regsiters
1. Device controller operates the device hardware to perform the data transfer
1. Driver may poll for status and data or DMA transfer into kernel memory
  1. In the case of a DMA cotnroller, an interrupt is generated when the transfer completes
1. The relevant interrupt handler receives the interrupt, signals device driver, and returns from interrupt
1. Device driver receives the signal from the interrupt handler, determines the request's status, signals the kernel I/O subsystem that the request has completed
1. Kernel transfers data / return codes to the address space of the requesting process and moves the process from the wait queue back to the ready queue
1. Moving the process to the ready queue unblocks the process
1. When the scheduler assigns the process to the CPU, process resumes execution at the completion of the system call
