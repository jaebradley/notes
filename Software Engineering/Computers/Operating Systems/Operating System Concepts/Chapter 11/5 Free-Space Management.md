# 11.5 Free-Space Management

## 11.5.1 Bit Vector

* Each free block is represented as a bit with value `1` and each allocated block is represented as a bit with value `0`
* If the second, third, and 5th blocks are free then the bitmap will look like `01101`
* Advantage of the bit vector is that it is relatively efficient to find the first free block or `n` consecutive free blocks using bit manipulation
* Sequentially check each "word" in the bit map to see whether that value is not `0`, since a `0`-valued "word" contains only `0` bits and represents a set of allocated blocks
* To find the first non-`0` "word", scan for the first `1` bit / location of the first free block
* Bit vectors are inefficient unless the entire vector is kept in main memory
  * Possible for smaller disk but not necessarily for larger ones
  * A 1-TB disk with 4-KB blocks requires 32MB to store its bit map

## 11.5.2 Linked List

* Keep pointer to first free block in a special location on disk and cache it in memory
* First free block contains a pointer to the next free disk block, etc
* Usually the operating system just needs a free block to allocate for a file, so the first block in the free list is used

## 11.5.4 Counting

* Generally, several contiguous blocks are allocated or freed simultaneously
  * Particularly, when space is allocated via contiguous allocation or clustering
* Instead of keeping a list of `n` free disk addresses, keep addresses of first free block and then a count / offset of the number of free contiguous blocks that follow the first block
* Entries can be stored in a B-tree, instead of a linked list for efficient lookup / insertion / deletion

## 11.5.5 Space Maps

* ZFS file system was designed to encompass huge numbers of files and directories
* At large scales, metadata I/O can have a large performance impact
  * If the free space list is implemented as a bit map, bit maps must be modified when blocks are allocated and when they are freed
  * Freeing 1GB of data on a 1TB disk could cause thousands of blocks in the bit map to be upsedated because those data blocks could be scattered over the entire disk
* ZFS creates metaslabs to divide the space on the device into chunks of manageable size
  * A volume may contain hundreds of metaslabs
  * Each metaslab has an associated space map
  * Space map is a log of all block activity i.e. allocations and freeing
    * Sorted in time order
* When ZFS allocates ro frees space from a metaslab, it loads the associated space map into memory in a balanced-tree structure
  * Replays the log into that structure
  * The in-memory space map is then an accurate representation of the allocated and free space in the metaslab
* ZFS combines contiguous free blocks into a single entity
* Free-space list is updated on disk as part of the transaction-oriented operations of ZFS
