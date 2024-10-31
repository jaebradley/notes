# Chapter 6. Matrix and Vector Computation

## Memory Fragmentation

* Python lists store pointers - i.e. they store locations where that data can be found
* For vector and matrix operations, performance can degrade because there are multiple lookups involved when fetching an element from the matrix
* Do a lookup for the inner list at index X in the outer list
* This returns a pointer to where the data for the inner list is stored
* Then there’s another lookup on the inner list at index Y for a pointer to where the data at that index is stored
* If the data was instead located in one contiguous block in memory, could move all of the data in one operation instead of needing two operations *for each element*
* When data is fragmented, each piece of data is moved over individually instead of moving the entire block over
* Invokes more memory transfer overhead, forcing the CPU to wait while data is transferred

## Understanding `perf`

* kernel suspends program when doing I/O (reading from memory, disk, network)
* When memory is allocated, kernel gives program reference to memory
* When the referenced memory is first used, the operating system raises a minor page fault interrupt
* This pauses the program and allocates the necessary nmemory
* Minor page faults are expensive, most of the related operations are done outside the scope of the running program
* Major page faults occur when program requests data from a device that hasn’t been read yet
* Even more expensive as they not only interrupt the program, they also read from the device
* Major page faults do not impact CPU-bound work as much as they impact programs that do many disk or network reads and writes
* Won’t get cache misses can be a source of slowdowns for CPU-bound work as we need to wait to fetch data from RAM
* CPU tries to guess which direction a logical branch will take and preload expected instructions
* Branch misses can result in strange effects like some loops running substantially faster on sorted lists than unsorted lists because there are fewer branch misses

## Making Decisions with `perf`’s Output

* Vectorization of computations is when the CPU executes multiple computations at the same time
* This can only happen if the CPU cache is filled with all the relevant data
* Memory bus only moves contiguous chunks of memory, so vectorization is only possible if the example grid data is stored sequentially in RAM
* Since lists store pointers to data instead of actual data, the actual values in the grid are scattered throughout memory and cannot be copied all at once
* The `array` module stores data sequentially in memory so that a slice of the `array` represents a contiguous range in memory
* Python does not know how to vectorize the loops over the (contiguous) `array` data
* Vectorized operations uses a different part of the CPU and different instructions than non-vectorized operations
* `array` has performance constraints associated with it - more sutiable for efficiently storing fixed-type data and less suitable for math

## Enter numpy

* The CPU is exceedingly fast, and most of the time simply getting it the data it needs faster is the best way to optimize code quickly
* Instruction count doesn’t *necessarily* correlate with performance - a program with fewer instructions may execute them inefficiently or they may be slow instructions
* `numpy` version of the program reduced cache misses
* Vectorization was a smaller optimization than reducing memory fragmentation

## Memory Allocations and In-Place Operations

* Allocations are more expensive than cache misses
* Request to operating system to find an available chunk of data and reserve it
* Non-in-place array operations are slower when the arrays used are bigger than the CPU cache
* When the arrays are smaller than the CPU cache, and the two inputs and output arrays can all fit into the CPU cache, the out-of-place operations are faster due to benefiting from vectorization
* Minor faults are caused when a program accesses newly allocated space in memory
* Since memory addresses are lazily allocated by the kernel, accessing this newly allocated data results in a call to the kernel
* The kernel pauses process execute and makes sure that the required memory exists and creates references to it for the executing program - expensive process
