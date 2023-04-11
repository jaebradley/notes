# 11.4.1 Contiguous Allocation

* Each file occupies a set of contiguous blocks on disk
* Disk addresses define a linear ordering on the disk
* If a file is `n` blocks long and starts at location `b` then it occupies blocks `b` to `b + n - 1`
  * Directory entry for each file indicates the disk address of the starting block and the length of the area allocated for the file
* When accessing blocks sequentially, the file system remembers the last block disk address
* To access some block `i` for a file, can directly access block `b + i`

## Problems

* Similar problems to fragmentation that occurs with contiguous memory
* As files are allocated and deleted, the free disk space is broken into little pieces
* Problematic when storage is fragmented into a number of holes, none of which is large enough to store the next request for data
* One solution is to copy entire file system onto another disk
  * Original disk is freed (one large contiguous space)
  * May take a long time (hours) and may need to be done on a frequent (weekly) basis
* Determining how much space a file needs can lead to problems
  * It may be difficult to make a file larger "in place" if "both sides" of a file is used
  * One way to support this is to find a larger hole, copy the contents of the file to the larger hole and release the previously occupied space
    * Can be time consuming to find this larger hole
* Initially, a contiguous chunk is allocated, but if this initial chunk is not large enough another chunk of contiguous space is added (an **extent**)
  * File's blocks are a disk address + a block count, as well as a link to the first block of the extent
