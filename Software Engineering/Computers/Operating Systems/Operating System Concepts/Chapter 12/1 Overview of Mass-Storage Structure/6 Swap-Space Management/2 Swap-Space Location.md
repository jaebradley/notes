# 12.6.2 Swap-Space Location

* Swap space can be part of the normal file system, or it can be in a separate disk partition
* If the swap space is simply a large file in a file system, normal file-system routines can be used to access / modify it
  * Inefficient because navigating the directory structure and the disk-allocation data structures takes time and potentially extra disk accesses
* Swap space can be created in a separate raw partition without a file system or directory structure
  * Swap-space storage manager is used to allocate / deallocate blocks from this raw partition
  * Manager optimizes for speed vs. storage efficiency since swap space is accessed much more frequently than file systems
  * Life of data in swap space is generally much shorter than that of files in a file system
* Some OSes can swap both in raw partitions and in file-system space
