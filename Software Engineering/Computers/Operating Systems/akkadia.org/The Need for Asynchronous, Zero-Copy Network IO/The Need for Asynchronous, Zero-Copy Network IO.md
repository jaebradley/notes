# [The Need for Asynchronous, Zero-Copy Network I/O](https://www.akkadia.org/drepper/newni.pdf)

* At the time of writing, the kernel would copy data from the network interface at least once internally before making the data available in a user-level buffer

## The Existing Implementation
* All I/O interfaces have the same problem: the caller provides the buffer where received data is stored
  * Network traffic arrives asynchronously, and this incoming data needs to be stored somewhere or it will be lost
* To avoid copying data more than once, it is necessary to have user readable buffers available the moment the data arrives
* With `aio_read` it is possible to specify these buffers ahead of time
  * The program needs to be notified about the arrival of data
  * The existing mechanisms use polling where the program can query the state of the request
    * Sending signals when data has arrived is expensive - sending thousands of signals a second that correspond to incoming packets
* With unreliable protocols, it might be more important to receive the latest data
  * Data that arrived earlier should be sacrificed for the latest information
* If the idea is that network hardware is supposed to transfer data directly into user-specified memory regions
  * Network hardware uses direct memory access to write into RAM instead of passing data through the CPU
  * This happens at a level below virtual address space management as DMA only uses physical addresses
* Buffers must remain in RAM until they are used
* User-level programs do not see physical RAM - virtual addresses are an abstraction and the operating system may decide to remove memory pages from RAM to make room for other processes
  * Buffers used for DMA must not be evicted from RAM and locked
  * Possible with `mlock`, but this is a privileged operation so that arbitrary processes can not lock down memory that would impact all the other processes on the system by starving resources

## Memory Handling
* Provide memory regions which are accessible at the user level and can be directly accessed by hardware other than the processor, like network cards and disk controllers
* It is necessary to prevent swapping these pages out by using `mlock`  for memory locking
  * Locking can only be implemented on a per-page basis, so locking one small object on a page locks the entire page
