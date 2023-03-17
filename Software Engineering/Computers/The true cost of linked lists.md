# [The true cost of linked lists](http://ykarroum.com/2022/05/30/true-cost-list/)

## Caches and locality

* Although CPU speeds have improved a lot recently, memory latencies have not
* L1 cache has ~0.5ns latency, L2 ~7ms, Main Memory (DRAM) ~100ns
* Spatial locality: when you access an address in memory, you'll probably need the content of the addresses closest to it
* Temporal locality: when you access an address in memory, you'll probably need the same value in the near future
* Vector heavily benefits from spatial locality
  * Retrieve first element from memory (~100ns)
  * CPU retrieves whole cache line from memory (64 bytes)
  * On next iteration of loop, try to access second element in vector, but that value is cached (~7ns)
* Linked list will see more cache misses since the next pointer in a linked list may not be the next address in the cache line / not necessarily part of the 64 byte cache line
  * If the linked list is held in contiguous memory, the vector equivalent is only 7 times faster and not 90 times faster

## What about inserting in the middle of the list?

* Vector is faster than linked list when inserting 4k+ elements
* This is due to the fact that when inserting in a linked list, the implementation uses memory allocation, and mallocs are costly
* People tend to focus on the cost of moving the whole vector just to insert one element, but in practice (unless you're doing it too often), the mallocs cost will still be greater
