# [What happens after a page fault?](https://unix.stackexchange.com/a/231188/459923)
* Page fault occurs when a memory access fails because the memory management unit (MMU) lookup for a virtual address ended in an invalid descriptor or in a descriptor lacking sufficient permissions (like a write attempt to a read-only page)
* When a page fault occurs, the processor generally
  * Switches to kernel mode
  * Sets register context values about the nature of the fault
  * Jumps to the memory address of the page fault handler

## Common Cases
* If the page triggering the fault is in swap, the kernel finds a spare physical page
  * Can also obtain a page by removing a page that contained disk cache or obtains a page by saving that page's contents to swap
  * Then it loads data from the page in swap to the obtained physical page
  * Change MMU table so that virtual address that caused the fault is associated with the obtained physical page
  * Kernel switches process to the instruction that caused the fault and instruction should execute successfully
* If the page is a copy-on-write page and a write access was attempted, the kernel will also attempt to obtain a spare physical page and copy data from the read-only page to the spare physical page
  * Kernel will also change the MMU descriptor and execute the process instruction again
* If the page is not mapped or doesn't have the requisite permissions, the kernel returns a SIGSEGV signal indicating segmentation fault to the process
  * Process execution resumes at the signal handler instead of the original location
  * Original process location that triggered page fault is saved on the stack
  * If the process does not have a segmentation fault handler, it is terminated
