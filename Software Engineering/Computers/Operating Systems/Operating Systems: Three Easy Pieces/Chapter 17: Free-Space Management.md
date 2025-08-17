# Chapter 17: Free-Space Management
* External fragmentation is when the space of available memory is allocated into little pieces of different sizes
* Future requests for memory may fail because there is no single contiguous spave to satisfy the memory request even though the total amount of free space exceeds the size of the request

## 17.1 Assumptions
* If a program calls `malloc` and is given a pointer to some space within the heap, that memory region is essentially "owned" by the program until the program returns it via a call to `free`
* Assumption (for now) is that the memory allocator manages a contiguous region of bytes
  * In some cases, allocator could ask for the region to grow
  * User-level memory allocation library might call the kernel to grow the heap, via a system call, when the program runs out of space

## 17.2 Low-level Mechanisms

### Splitting and Coalescing
* Free list contains set of elements that describe the memory still remaining in the heap

```
# Example 30 bytes heap
|  free  |  used  |  free  |
0        10       20       30

# Free list
head -> { address: 0, length: 10 } -> {address: 20, length: 10 } -> NULL
```

* Request for a single byte of memory
* Allocator will "split" a free chunk of memory that can satisfy request for single byte of memory
* Need to also coalesce free space when memory is freed
 * When the used memory between bytes 10 and 20 is freed, if the space is naively added back to the free list, then it might look like there are three chunks of 10 bytes each
 * `head -> { address: 10, length: 10 } -> { address: 0, length: 10 } -> {address: 20, length: 10 } -> NULL`
 * Thus, if a user requests 20 bytes, a naive list traversal will not find a free chunk
 * Coalescing involves looking at the addresses of nearby chunks of free space and merging existing free chunks into a single larger free chunk

### Tracking The Size of Allocated Regions
* Most allocators store extra information in a header block, kept in memory, just before the returned chunk of memory
* When a user requests `N` bytes of memory, the allocator doesn't search for a free chunk of size `N` but rather a free chunk of size `N` + `size of header`
