# [What they don’t tell you about demand paging in school](https://offlinemark.com/demand-paging/)
* Kernel returns a pointer when `mmap`ping an anonymous page
* Kernel waits until process triggers a page fault by writing the memory before doing any real memory allocation
  * This is called "demand paging"
* Unlike writes, reads to a new `mmap`ped region do not trigger a memory allocation
  * Instead of allocating memory, kernel responds to the page fault by using the zero page 
  * Zero page is a pre-allocated page of physical memory filled with zeros
* `mmap` could proactively allocate the page table entry and initialize it to the zero page
  * This would avoid the page fault on first read, but at the cost of initializing many page table entries up front

## Allocating infinite memory, and touching it too
* Author set up an experiment that allocated 100GB of linear memory and tried to read the first byte of each page
* `pmap` confirms that there's a 100GB region of anonymous virtual memory
* `htop` also confirms that 100GB of virtual memory is allocated but that a much smaller amount of resident memory (1540 KB) is occupying RAM
* Many minor page faults
  * Minor page fault is a page fault that does not require loading from disk
* A sparse array is typically a very large array whose elements are mostly zero
  * Can implement an memory efficient sparse array where the majority of the array is backed by the zero page

## The hidden overhead of page tables
* Tree data structure that has a few levels (four levels on `x86_64`)
  * Each node (table) is an array of 512 `8`-byte entries
  * Tables represent a mapping between every virtual page in the address space and physical frame
* Each "touched" page requires a page table entry
* PTEs aren't allocated individually, but rather allocated in blocks of 512 "Page Tables"
* Page Tables aren't allocated individually - they're allocated in blocks of 512
* Each Page Table also requires a "Page Directory Entry"
* Page Directory Entries are allocated in blocks of 512 "Page Directories"
* Only one Page Map Level 4 Table (that points to the Page Directories)
* PTEs are 8 bytes and all page tables contain 512 entries, so each node is 4096 bytes (4KB)
* To map a single page, need one Page Map Level 4 Table, one Page Directory, one Page Directory Entry, one Page Table that are all 4KB each for a total of 16KB
* So author re-ran previous experiment (which was on a machine with 1GB of RAM) but instead of allocating 100GB allocated 512GB
  * This lead to an OOM killing the process due to page table overhead
  * Resident memory was extremely low
  * Huge levels of minor fage faults
  * Close to 800MB of memory was used by page tables (close to physical memory limit)
  * From the kernel stack trace, there was an allocation to map another zero page which ended up triggering the out of memory
  * So this operation is not "free"
