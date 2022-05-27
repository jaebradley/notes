# [Virtual Memory: The Details](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/4/html/introduction_to_system_administration/s1-memory-virt-details)

* Virtual address space is the maximum amount of address space available to an application
* Virtual address space depends on the architecture because it is the architecture that defines how many bits are available for addressing purposes
* The virtual address space is the total number of uniquely addressable memory locations and NOT the amount of physical memory installed in the system
* Without an MMU, when the CPU accesses RAM, the actual RAM locations never change - memory address 123 is always the same physical location within RAM
* With an MMU, memory addresses go through a translation step prior to each memory access
  * Memory address 123 might be directed to physical address 82043 at one point in time and physical address 20468 at another time
  * MMU divides RAM into pages (contiguous sections of memory with a set size) as individually tracking the virtual address / physical address translation would be very expensive
  * If application has a 150000 byte virtual address space, and the application's first instruction is at address 12345, but the computer has only 12288 bytes of physical RAM, when the CPU attempt to access address 12374, a page fault will occur.
