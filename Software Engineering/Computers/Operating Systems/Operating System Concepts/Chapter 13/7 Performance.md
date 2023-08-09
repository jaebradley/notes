# 13.7 Performance

* I/O uses the CPU to execute device-driver code and to schedule processes fairly and efficiently as they block and unblock
* Context switches stress the CPU and its hardware caches
* Memory bus is used for copying data between controllers and physical memory and again during copies between the kernel buffers and the application data space
* Interrupts are expensive
  * Involve a state change
  * Execution of the interrupt handler
  * State restoration

## Network Traffic Example
