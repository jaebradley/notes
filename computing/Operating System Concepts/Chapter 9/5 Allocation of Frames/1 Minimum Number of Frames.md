# 9.5.1 Minimum Number of Frames

* Cannot allocate more than the total number of available frames
* Also must allocate a minimum number of frames
* If the number of allocated frames decreases, the page-fault rate increases (as the probability of encountering a not-yet-allocated frame increases)
* When a page fault occurs, the instruction is restarted
  * Must have enough frames to hold all the different pages that any single instruction can reference
  * A `load` instruction on page 16 can refer to an address on page 0, which is an indirect reference to page 23, thus, at least 3 frames are required to execute the load instruction
* If a computer architecture allows multiple levels of indirection, can have indirect addresses that refer to other indirect addresses, etc.
  * Theoretically, if this chain of addresses referenced every page in virtual memory, then all of the virtual memory would be in physical memory
  * Must limit the amount of indirection by having a counter increment on each successive indirect reference for a given instruction
  * If some limit is reached, a trap occurs. This limit value determines the maximum number of memory references per instruction (and the thus, the maximum number of frames)
