# Virtual Memory

* The VM subystem of a processor implements the virtual address spaces provided to each process
  * This makes the process think it is alone in the system
* A virtual address space is implemented by the Memory Management Unit
  * The OS has to fill out the page table data structures - the CPUs do the rest of the work themselves
* The input into the MMU to perform address translation is a virtual address
  * Virtual addresses are 32-bit or 64-bit values
