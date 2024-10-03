# Chapter 3. Lists and Tuples

* System memory on a computer can be thought of as a series of numbered buckets, each capable of holding a number
* Python stores data in these buckets by reference, which means the number itself points to ("references") the data we actually care about
* Thus, these buckets can store any type of data we want
* When creating a list or tuple, we have to allocate a block of system memory, where each "bucket" or section in the block will contain an integer pointer to the actual data
  * To allocate this memory, the system kernel is called to request access to X consecutive buckets
  * Note that in Python, lists also store how large they are, so if there are `5` elements in a list, there will actually be `6` allocated buckets where the zeroeth bucket contains the length of the list

## Lists Versus Tuples

* Tuples are cached by the Python runtime, which means that we don't need to talk to the kernel to reserve memory every time we want to use one
* Both lists and tuples can take mixed types

### Lists as Dynamic Arrays

* When an array is resized, Python creates a new list that is big enough to hold original list, plus some additional headroom for future appends
* Data from the old list is copied to the new list and then then old list is destroyed
* One append is probably the beginning of many appends, and by requesting extra space, reduce the number of times this allocation occurs
  * Reduce total number of memory copies, which is important since memory copying is pretty expensive, expecially when list sizes start growing
* Example where a 100k element list uses 2.7x memory when building list using `.append` vs. list comprehension
  * Ultimately, the runtime is slower because the extra Python statements that are executed as well as the additional memory reallocation / copying

### Tuples as Static Arrays

* Tuples of sizes `1-20` may not be immediately garbage-collected - up to 20k of each size are allocated for future use
* So when a new tuple of a certain size in this range is neede din the future, don't necessarily have to communciate with the operating system to find a region in memory to put the data in, since a certain number of these tuples were already reserved
