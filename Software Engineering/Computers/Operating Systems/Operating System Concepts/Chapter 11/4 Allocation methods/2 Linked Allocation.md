# 11.4.2 Linked Allocation

* Each file is a linked list of disk blocks
* Disk blocks are scattered anywhere on disk (i.e. blocks are probably non-contiguous)
* Each disk block contains a pointer to the next disk block
  * If each disk block is `512` bytes in size, and a disk address / pointer is `4` bytes, then the user sees blocks of `508` bytes
* File creation involves creating a new entry in the specified directory
* The directory entry contains the pointer to the first disk block (initially set to `nil` to signify an empty file)
* This implementation results in the following advantages
  * No external fragmentation as any free block on the free-space list can be used
  * File size does not need to be declared when the file is created - files can grow as long as blocks are available
    * Compaction is no longer necessary as generating the free contiguous space from compaction is no longer needed
* Linked access allocation does not effectively support sequential access
  * i.e. to get to the `i`th block of a file, all prior blocks must be traversed
* If a pointer requires `4` bytes and blocks are `512` bytes large, then `0.78` percent of the disk is used for pointers
  * Solution is to collect blocks into "clusters", and allocate "clusters" instead of blocks
  * Increase in internal fragmentation, as more space is wasted when this collection of blocks is partially full than when a block is partially full
