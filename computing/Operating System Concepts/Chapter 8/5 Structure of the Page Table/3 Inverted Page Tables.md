# 8.5.3 Inverted Page Tables

* Usually, each process has an associated page table
  * Page table has one entry for each page that the process is using, or one slot for each virtual address, regardless of the virtual address's validity for the given process
  * Drawback is that page table may consist of millions of entries, ironically consuming a large amount of physical memory to keep track of how other physical memory is being used
* Inverted page tables have one entry for each frame of memory
* Each entry consists of the virtual address of the page stored at the physical memory address, and information about the process that owns that page
* Inverted page tables often require an address-space identifier be stored in each entry of the page table
  * This is because the mapping for several different processes is contained within this single page table
  * In a simpolified example, can use the process id as the address space identifier such that the page table key looks something like
    * `| process id | page number | offset |`
* The inverted page table is searched for a match, given the logical address
  * If a match is found at entry I, then the physical address I + offset is generated
  * If no match is found, an illegal address access has been attempted
* Because the entire page table may need to be searched for a match, the inverted page table approach adds a time cost
  * Can combine a hash table + inverted page table to limit search to, at most, a few page-table entries
  * Each hash table access adds a memory reference
  * TLB is searched before the hash table is consulted
