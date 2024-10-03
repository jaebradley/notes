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
