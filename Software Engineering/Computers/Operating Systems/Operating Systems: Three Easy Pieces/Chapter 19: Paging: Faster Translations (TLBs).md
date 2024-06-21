# Chapter 19. Paging: Faster Translations (TLBs)

* TLB is part of chip's MMU
  * Hardware cache of popular virtual address translations for physical addresses
* Each virtual memory reference asks the hardware cache (TLB) to see if a translation exists in the cache for the physical address associated with the specified virtual address
* THus the translation is performed (quickly) without having to consult the page table (which contains all the virtual address translations)

## TLB Basic Algorithm

* Extract the virtual page number from the virtual address
* If the TLB has the VPN (TLB hit), then the TLB holds the translation for the virtual address
* Extract the page frame number from the relevant TLB entry, combine the PFN with the virtual address offset, and form the physical address
* On a TLB miss, hardware accesses the page table to find the relevant physical address
* TLB miss is expensive because accessing the page table needs extra memory references
  * Memory accesses, relative to most CPU instructions are quite costly
* Adds the physical address for the specified virtual address
* Hardware retries finding the virtual address - which should lead to a TLB hit

## Example: Accessing an Array

* Array of ten 4-byte integers in memory
  * Virtual address starts at 100
* 16-byte pages
* 8-bit virtual address space
  * 4-bit offsets since there are 16 bytes on each page
  * 4-bit page number (aka VPN) so 16 total virtual pages
* Array elements are split between virutal page 6 to page 8
* First array element is accessed
* CPU sees the virtual address `100`
* Hardware extracts the virtual page number from the virtual address
* Checks to see if the VPN (`6`) is in the TLB
* Because this is the first memory access, it is not found and results in a TLB miss
* Next array element results in a TLB hit as the second array element lived on the same page as the first element
* This page was loaded into the TLB when processing the TLB miss for the first element
* At some point, an array element will have a VPN of `7` and this will result in a TLB miss, and then a lookup for page `7` will occur
* Note there's a relationship between the page size and the TLB miss rate for dense array-based accesses
  * If the page size was 2x (32 bytes vs. 16 bytes) the number of TLB misses would be halved
