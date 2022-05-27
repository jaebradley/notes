# [Virtual Memory: The Details](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/4/html/introduction_to_system_administration/s1-memory-virt-details)

* Virtual address space is the maximum amount of address space available to an application
* Virtual address space depends on the architecture because it is the architecture that defines how many bits are available for addressing purposes
* The virtual address space is the total number of uniquely addressable memory locations and NOT the amount of physical memory installed in the system
* Without an MMU, when the CPU accesses RAM, the actual RAM locations never change - memory address 123 is always the same physical location within RAM
* With an MMU, memory addresses go through a translation step prior to each memory access
  * Memory address 123 might be directed to physical address 82043 at one point in time and physical address 20468 at another time
  * MMU divides RAM into pages (contiguous sections of memory with a set size) as individually tracking the virtual address / physical address translation would be very expensive
  * If application has a 150000 byte virtual address space, and the application's first instruction is at address 12345, but the computer has only 12288 bytes of physical RAM, when the CPU attempt to access address 12374, a page fault will occur.

## Page Faults

* Sequence of events occurring when a program attempts to access an address space that is not currently located in the system's RAM
* The operating system must handle page faults by making the accessed data memory resident and allowing the program to continue operation as if the page fault had never occurred
* CPU first presents virtual address 12345 to the MMU
 * MMU has no translation for 12345, it interrupts the CPU and causes software (the page fault handler) to execute
 * The page fault handler can 
  * find where the desired page resides on disk, and read it
  * determine that the desired page is already in RAM, but not allocated to the current process, and update the MMU to point to the desired page
  * Point ot a special page containing only zeros, allocate a new page for the process only if the process ever attempts to write to the copy on write page

## The Working Set

* The group of physical memory pages dedicated to a specific process is known as the working set for that process
* Working set grows as a process page faults
* Pages must be removed from process's working set and turned into free pages
* Operating system shrinks processes's working sets by writing modified pages to a dedicated area on a mass storage device (swapping / paging space)
* Marking unmodified pages as being free as there's no need to write pages to disk if they have not changed

## Swapping

* Swapping can lead to thrashing where pages from a process are swapped
 * The process becomes runnable and attempts to access a swapped page
 * The page is faulted back into memory, probably forcing some other processes's pages to be swapped out
 * The page is swapped out again
* Indicative of insufficient RAM for the present workload
* CPU and I/O loads that are generated can outweigh the load imposed by the system's real work - in the most extreme cases, there may be no real work as all resources are spent moving pages to / from memory
