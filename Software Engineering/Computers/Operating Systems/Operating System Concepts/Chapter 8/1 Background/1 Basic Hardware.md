# 8.1.1 Basic Hardware

* CPU can only directly access main memory and registers built into the processor
  * Machine instructions take memory addresses as argumentsbut none take disk addresses
* Any instructions in execution, and any data being used by the instructions must be in main memory, or moved from disk to main memory before the CPU can operate on them
* Registers are generally accessible within one cycle of the CPU clock
* Completing a memory access may take many cycles of the CPU clock
  * Processor normally needs to stall
  * A cache is added between the CPU and main memory to avoid the frequency of memory accesses / stalls

## Memory Space Protection

* Each process has a separate memory space
* Protects the operating system from access from user processes
* Protects user processes from other user processes
* Base and limit registers hold the smallest legal physical memory address, and the limit register specifies the size of the range
* CPU compares every address generated in user mode against this range of addresses
  * If an address is outside the legal memory address space, the operating system will fatally error
* Only the operating system loads the base and limit registers
* Operating system, executing in kernel mode, is given unrestricted access to both operating system and user memory
