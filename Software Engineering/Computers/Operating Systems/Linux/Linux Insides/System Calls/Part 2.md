# [System calls in the Linux kernel. Part 2.](https://0xax.gitbooks.io/linux-insides/content/SysCall/linux-syscall-2.html)

## Initialization of the system calls table

* System calls are implemented as software interrupts
* When the processor handles a syscall instruction from a user appolication. this instruction cuases an exception which transfers control to an exception handler
* The linux kernel searches for the address of the necessary system call handler via the system call table (`sys_call_table` array)
* To start with, all elements of the system call table array point to the not-implemented system call which returns an error
  * The `ENOSYS` error tells us that the function is not implemented
* Certain elements of the system call table array are then assigned their specific handlers (the 0th element is assigned to sys_read, the 1st element is assigned to sys_write, etc)
* When the Linux kernel gets control to handle an interrupt it has to do things like save user space registers, switch to a new stack, etc, before calling the interrupt handler, which is similar to what needs to happen for system call handling

## Preparation before system call handler will be called

* When a system call occurs from the user's application, general purpose registers have things like the system call number, the return address to the user space, register flags, and the arguments to the system call handler
  * These registers are pushed to the top of the stack
* There are some checks to validate the system call number, then the `call` instruction is executed with the address of the appropriate system call handler

## Exit from a system call

* After returning from a system call handler, put the return value of a system handler on to the stack
* The system call will return the result to the user program in the general purpose `rax` register, so move the value on to the stack after the system call handler has finished working
* Restore the general purpose registers and use `systretq` instruction to exit
