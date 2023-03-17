# [Interrupts and Interrupt Handling. Part 1.](https://0xax.gitbooks.io/linux-insides/content/Interrupts/linux-interrupts-1.html)

## What is an Interrupt?

* An interrupt is an event raised by software or hardware when it needs the CPU's attention
* Assume that each peripheral device has an intterupt line to the CPU
* New machines have an APIC (Advanced Programmable Interrupt Controller)
  * The APIC consists of a Local APIC (which is locaated on each CPU core) and an I/O APIC
* When an interrupt occurs the operating system must ensure the following steps:
  * The kernel must pause execution of the current process
  * The kernel must search for the handler of the interrupt and transfer control
  * After the interrupt handler completes execution, the interrupted process can resume execution
* There are external / hardware-generated interrupts and software-generated interrupts
  * External interrupts are received through the local APIC while the software-generated interrupts are caused by exceptional conditions in the processor itself
  * Examples of an exceptional condition is division by 0 or exiting a program with a syscall instruction
* If multiple exceptions or interrupts occur at the same time, the processor handles them in the order of their predfined priorities
* The entries in the Interrupt Descriptor Table contains the type of interrupt handler (handling interrupts, traps, or tasks), as well as the address of the handler itself
* There is the Interrupt Stack Table that is used for switching stacks during the execution of an interrupt or exception handler
* Each active thread has a large stack (2^15 or 2^16 bytes, depending on if a kernel configuration parameter is defined)
* Each CPU has special per-cpu stacks like the interrupt stack, which is used for external hardware interrupts
* After the interrupt handler finishes its execution, it must return control to the interrupted process, which also pops the stack pointer to restore the stack of the interrupted process
