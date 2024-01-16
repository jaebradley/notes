# Chapter 17: Free-Space Management

* External fragmentation is when the space of available memory is allocated into little pieces of different sizes
* Future requests for memory may fail because there is no single contiguous sapce to satisfy the memory request even though the total amount of free space exceeds the size of the request

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
```
