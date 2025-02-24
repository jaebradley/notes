# [Linux kernel space and user space](https://stackoverflow.com/questions/17208648/linux-kernel-space-and-user-space)

* There's only one "kernelspace" for the whole machine and all processes share it
* When a process is active, it can either be running in user mode or kernel mode
* In user mode, the instructions being executed by the CPU are in the userspace side of the memory map
* In user mode, a process has limited abilities, like not being able to use privileged instructions
* When a process wants to do something other than move data around in its own userspace virtual memory, like open a file, it makes a syscall
* Syscall is basically a magic instruction that turns on privileged mode and jumps to a special address in kernelspace, the syscall "entry point"
  * Process is running in kernel mode
  * Instructions being executed are located in kernel memory, and they can read and write any memory they want
  * So for `open("foo", O_RDONLY)` where `"foo"` is part of your program in userspace, the syscall mechanism only passed a pointer, not the entire string, so the kernel reads the string from user memory
  * If request can't be satisfied immediately, kernel may put process to sleep until a response is received from disk or network
  * Another process may get a chance to run
  * Response occurs and process starts running again, in kernel mode
  * The `open` syscall finishes and returns to userspace
* Return to userspace puts the CPU back in non-privleged mode and restores registers to what they were before the transition, with the instruction pointer pointing at the instruction after the magic syscall instruction
* If process accesses virtual memory address that doesn't have a physical address associated with it, CPU enters kernel mode and jumps to the page fault handler
* If some hardware (network, disk) notifies the CPU that it requires attention, the CPU enters kernel mode and jumps to a handler, then resumes userspace process that was running before the interrupt
