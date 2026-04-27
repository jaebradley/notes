# Measurements of Cache Effects

* Test program that builds a linked list of entries that are sequential or random, advancing entries using a pointer
  * Each entry contains a payload that is an array of varying size

## Single Threaded Sequential Access

* Simple walk over all entries in the list
* Entries are sequential
* Processor can deal with processing forwards or backwards equally well
* Measuring how long it takes to handle single list element in processor cycles
* See three distinct levels when it comes to measuring cycles per list element against working set size (in bytes)
  * The size of the elements in the array is `0`
  * At 2^14 bytes (~4-5 cycles)
  * At 2^15 to 2^2 bytes (~8-9 cycles)
  * 2^21 bytes and up (9 cycles)
  * This is because the processor has a 16kb L1d and 1 MB L2 cache
* L2 cache times are lower than expected (9 cycles vs. expected time of 14 cycles to access L2) because in anticipation of using consecutive memory regions, processors prefetch the next cache line
  * When the next line is actually used, it is already halfway loaded
  * The delay required to wait for the next cache line to be loaded is therefore much less than the L2 access time
  * Main memory access takes 200+ cycles, however, with prefetching, when the working set size grows beyond the L2 cache size, the access time is still ~9 cycles
* In the cases where the size of the elements in the array for each entry varies (`0`, `56`, `120` and `248` bytes), the L1d performance looks roughly the same across all cases (cycles per list element, until 2^15 bytes)
  * At the L2 cache level, the non-`0` byte performances are the same, at ~28 cycles per list element
    * This implies that prefecthing from L2 into L1d is disabled, since ~28 cycles is the access time for the L2
    * With `NPAD` `7`, a new cache line is needed for each iteration of the loop
    * For `NPAD` `0`, the loop needed to iterate 8 times before the next cache line is needed
  * When the working set size exceeds the L2 capacity, there is a variance in performance as the different element sizes lead to differences in performance
  * The element size hampers performance as prefetching cannot cross page boundaries at a hardware level
  * The TLB cache is a cache where the results of the translation of a virtual address to a physical address are stored
    * The TLB cache is quite small, since it has to be very fast
    * If more pages are accessed repeatedly than the TLB cache has entries for, the calculation of virtual to physical address has to be repeated (which is a costly calculation)
    * If more pages are accessed repeatedly (which occurs with larger elements), TLB lookups will happen more often
* To see the TLB effects, a test is executed where each list element is put on a separate page vs. `NPAD` `7` where each element occupries one entire cache line
  * So for "regular" measurement, each list iteration requires a new cache line and for every 64 elements, will require a new page
  * For the second measurement, where each list item is on a separate page, each iteration requires loading a new cache line and a new page
  * Since physical address has to be computed before a cache line can be read for either L2 or main memory, the address translation penalties are additive to memory access times
  * This means that at certain sizes, the access time for an element might be higher than the theoretical access time for RAM alone
* To see affect of prefetching, test which compares the typical list walk, with walking the list, but incrementing the first array member of the current entry, with walking the list, but incrementing the first array member with the first array member of the next element
  * The "add next" test, in some cases faster than the simple incrementing test because the addition combination using the next element basically forces a prefetch
  * Whenever the "add next" program advances to the next list element, that list element must already be in the L1d cache
  * However, the "add next" program runs out of L2 faster than the incrementing test as it needs more data loaded from main memory
  * For the two tests that modify memory (i.e. increment the value of an array member in some way), an L2 cache eviction must also result in a write to memory, increasing the time it takes to transwer the data from main memory to L2

## Single Threaded Random Access

* At large working set sizes, random access reaches 450+ cycles per list element
* The curve for random access cost (cycles per list element by working set size) increases as the working set size increases
* When the working set size is larger than the L2 size, the cache miss ratio grows (until it reaches almost 100%)
* For random access, the per-element access time more than doubles for each doubling of the working set size
  * For example, from a working set size of 2^21 to 2^22, the L2 accesses per iteration jump from 72.2 to 174.0
  * For sequential, the L2 accesses per iteration for 2^21 to 2^22 jumped from 5.5 to 10.9
  * Attributable to TLB misses
* Test where a set of N pages are randomized (so for example, all list elements in a block of 60 pages are randomized and are traversed before going over to an element in the next block of 60 pages)
  * The number of TLB entries that are used at any one time is limited to N
  * Element size with `NPAD` of `7` (which corresponds to cache line size)
  * Since the randomized order of list elements in a block should not be impacted by the prefetcher, this also implies that the L2 cache miss rate should not differ significantly from the randomization of the entire list of elements in a single block (the "true" ranodmization of elements)
  * This implies that the main factor impacting performance is TLB misses, as performs decreases with the number of N blocks within a given set that entries are randomized in
