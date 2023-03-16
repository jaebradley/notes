# 8.4.2 Hardware Support

* Most operating systems allocate a page table per process
  * A pointer to the page table is stored with the other register values in the process control block
  * When restarting a process, it must reload the user registers and define the correct hardware page-table values from the stored user page table
* Simplest implementation is to use a set of dedicated registers
  * Must be performant as every access to memory must go through this paging map
  * Is fine if the page table is reasonably small, but most modern computers have page tables with millions of entries and this implementation strategy is not feasible
* Another implementation: page table is kept in main memory, and a page-table-base register points to the page table
  * Changing page tables involves changing this one register
  * To access a user memory location
    * Need to index into the page table, using the value in the PTBR - this requires a memory access
    * Identify the frame number and page offset to produce physical address
    * Access byte using physical address - another memory access

## Translation Look-Aside Buffer (TLB)

* To avoid doubling the memory accesses, use associative, high-speed memory cache - the translation look-aside buffer
* Each entry in the TLB is a key / value pair
* When doing a lookup for a key in the TLB, all keys are compared simultaneously
* Hardware is expensive, so entries in the TLB are between 64 and 1,024
* TLB contains only a few of the page-table entries (mapping of page numbers to frame numbers)
* If a page number is found, the frame number is immediately available, with the byte lookup taking 10% of the time an unmapped memory reference would take
* A TLB miss will require a "full" memory access - lookup the frame in the page table, lookup the physical address in main memory
  * After TLB miss, add the page number / frame number pair to the TLB
* Some TLBs store an address-space identifier in each entry
  * These ASIDs uniquely identify each process and are used to provide address-space protection for that process
  * When the TLB attempts to resolve page numbers, if the currently running process does not have the same ASID as the ASID associated with the page number entry, it treats the lookup attempt as a cache miss
  * Allows TLB to contain entries for different processes simultaneously
  * Avoids TLB flushing on context switch / every time a new page table is selected
    * In other words, if the TLB did not support ASIDs, every time a page table is selected, the TLB must be flushed
    * If the TLB is not flushed, it may contain old entries that contain valid addresses but have incorrect or invalid physical addresses left over from the previous process
* TLB lookup might take 20 nanoseconds, accessing memory might take 100 nanoseconds
