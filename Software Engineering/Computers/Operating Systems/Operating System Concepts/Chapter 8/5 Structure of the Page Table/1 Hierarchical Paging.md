# 8.5.1 Hierarchical Paging

* Consider system with 32-bit logical address space
  * If the page size is 4KB, then the page table consists of 1 million entries (2^32/2^12)
  * If each entry consists of 4 bytes, process would need 4MB of physical address space for just the page table

## Two-level Page Table

* `| first page number (10 bits) | second page number (10 bits) | page offset (12 bits) |`
* Use the first page number (highest 10 bits) to lookup the next / "inner" page table in the "outer" page table
  * This lookup will provide location of the next page table
* Use the second page number as the index into the "inner" page table
  * This lookup will provide the physical address frame address (which can then be combined with the page offset value)
* For a 64-bit logical address space, a two-level paging scheme does not make sense
  * If we assume a 4KB page size, a single-level page table would consist of up to 2^52 entries
  * In a two-level paging scheme, the outer page would consist of 42 bits, the inner page would consist of 10 bits and the offset would be 12 bits
  * Outer page is still very large, and would still be large even when subdividing the outerpage's 42 bits further
  * Ultimately, 7 levels of paging might be necessary, which is a prohibitive number of memory accesses
  * For 64-bit architecutre, hierarchical page tables are generally inappropriate
