# 11.4.3 Indexed Allocation

* Linked allocation cannot efficiently support direct access, since the pointers to blocks are associated with the blocks
  * And the blocks are scattered all over the disk, and must be retrieved in order
* Each file has its own index block, which is an array of disk block addresses
* Each `i`th  entry in the index block points to the `i`th block of the file
* Directory contains the address of the index block
* When a file is created, all pointers of the index block are set to `nil`
* When a block is first written, a block is obtained from the free-space manager and its address is put in the `i`th index block entry
* Indexed allocation does not suffer from external fragmentation because any free block on the disk can satisfy a request for more space
* With a file with only one or two blocks, there is wasted space
  * An entire index block must be allocated, if only one or two pointers will be used / non-`nil`
* Strategies for supporting large files, while also not creating very large index blocks by default
  * Link together several index blocks
  * Multilevel indices
    * First-level index blocks point to second-level index blocks, which point to the file block addresses
    * Two levels of indices with `4kb` blocks, could store a file up to `4GB`
  * Combined
    * First 12 (of 15) pointers in an index block point to direct blocks
      * "direct blocks" = addresses of blocks that contain the file's data
      * `4kb` block size means that `48kb` of data can be accessed "directly"
    * The 13th, 14th, and 15th pointers are pointers to "indirect" blocks
      * 13th pointer is a single indirect block, which is an index block that contains addresses of blocks that contain data
      * 14th pointer is a double indirect block, which contains the addresses of a block that contains the addresses of blocks that contain pointers to the actual data blocks
      * 15th pointer is a triple indirect block
    * With 64-bit file pointers, files up to TB in size can be supported
