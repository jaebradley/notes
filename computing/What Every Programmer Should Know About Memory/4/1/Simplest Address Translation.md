# Simplest Address Translation

* The MMU can remap addresses on a page-by-page basis
* Just like cache lines, virtual addresses are split into distinct parts
  * These parts are used as indices in various tables which are then used to construct the related physical address
* A Virtual Address is comprised of data that is able to select an entry in a Page Directory
  * The page directory entry determines the address of a physical memory page
  * More than one entry in the page directory can point to the same physical memory page
* The complete physical address of the memory cell is determined by combining the page address from the page directory with the low bits from the virtual address
* The data structure for the page directory is stored in main memory
  * The OS allocates contiguous physical memory for the page directory
  * The virtual address bits are used as an index into the page directory (an array of directory entries)
* 4MB pages on x86 machines
  * 22 bits to address every byte in a 4MB page (this is used to get a physical address for a physical page)
  * Remaining 10 bits are used to select one of the 1024 entries in the page directory
  * Each page directory entry contains a 10 bit base address of a 4MB page, which is combined with the offset to form a complete 32 bit address
