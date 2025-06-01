# [Log-structured File Systems](https://pages.cs.wisc.edu/~remzi/OSTEP/file-lfs.pdf)
* In-memory data caches are increasing in size
* This means that a greater percentage of disk traffic is write traffic vs. read traffic
* File system performance is largely determined by write performance
* Random I/O performance is much less performant than sequential I/O
  * Using disks in a sequential manner will lead to larger performance advantages
* The Unix File System performans a large number of writes when creating a new file with a size of one block
  * Writes a new indoe
  * Writes updates to the inode bitmap
  * Writes to the directory data block for the file
  * Writes to the new data block that is part of the new file
  * Writes to the data bitmap to mark the data block as allocated
* When writing to disk, log-structured file systems buffers all updates (including metadata) in an in-memory segment
  * When the segment is full, the segment is written to disk in one long, sequential transfer
  * Never overwrites existing data
 
## Writing Sequentially and Effectively
* Two sequential writes at address `A` and `A + 1` are not necessarily performant because the second write has to wait for the disk to rotate to the appropriate location
* Instead, contiguous writes are necessary to achieve good write performance
* LFS keeps track of updates in-memory
  * After receiving a sufficient amount of updates, it writes the updates to disk all at once
  * The set of in-memory updates is known as a segment
 
![Screenshot 2025-06-01 at 18 00 57@2x](https://github.com/user-attachments/assets/901cc289-48ea-416c-b319-22c24b0a9024)

## Finding Inodes
* Unix file system keeps all inodes at fixed portions of the disk
  * Given an inode number and a starting address, the inode's exact disk address can be calculated by multiplying the inode number by the size of an inode
* More difficult in LFS because inodes are scattered throughout the disk and because data is never overwritten, the latest version of an inode keeps moving

## The Inode Map
* Mapping between the disk address of the most recent version of the inode and the inode's number
  * Often implemented as an array with a 4 byte disk pointer per entry
  * Any time an inode is written to disk, the mapping is updated with its new location
* LFS places chunks of the inode map next to where it appends other data
  * When appending data to a file, LFS writes the new data block, its inode, and a piece of the inode map togther onto the disk
 
![Screenshot 2025-06-01 at 18 08 42@2x](https://github.com/user-attachments/assets/4e626ee6-f910-4fff-b59b-d668f1505b0c)

## Checkpoint Region
* How to find the inode map, given that it is spread across disk storage?
* LFS has a fixed place on the disk (the checkpoint region) that contains poitners to the latest pieces of the inode map
* The checkpoint region is only updated periodically, and has limited impact on performance

## What About Directories?
* LFS also adds directories sequentially to the segment (and thus, to disk)
* Inode map contains information for the directory file
* To read `som/directory/file`
  * First read the inode map (which should be cached in-memory) to find the location of the inode of directory `/some/directory`
  * Directory inode gives location of directory data
  * Reading the directory data block gives the name-to-inode number mapping of the `file`
  * Check inode map again to find the location of the inode number associated with `file`
  * Read the data block at this location
* Because the directory uses the file _name_ instead of the file's inode the directory data block doesn't need to be updated if a file's inode location changes

## Garbage Collection
* If the LFS garbage collector naively freed single data blocks and inodes, the file system would be comprised of free holes mixed with allocated disk space
  * Write performance would decrease as LFS would not be able to find a large contiguous region to write to disk sequentially
* Periodically, the LFS garbage collector reads through old segments and determines which blocks are still used within these segments
  * It then writes new segments with just the live blocks within them
  * The old segments are then freed

## Determining Block Liveness
* At the head of each segment is a segment summary block
  * This summary block includes the inode number and offset within the file for each data block
* For a block on disk at some address, look in the segment summary block and find its inode number and offset
  * In the inode map, find where the inode lives and read the inode data
  * If the offset location in the inode data matches the block's address on disk, then the block is live
  * If the offset location in the inode data matches another address, the block is not in use
* When a file is truncated or deleted, LFS increases its version number and records the new version number in the inode map
* By recording the version number in the on-disk segment, LFS can short circuit the longer check by comparing these versions

## Crash Recovery And The Log
* LFS keeps two checkpoint regions at opposite ends of the disk, writing to them alternatively
* When updating the checkpoint region
  * LFS writes a header including a timestamp
  * Then it writes the body of the checkpoint region
  * Then it writes one last block with a timestamp
* If the system crashes during a checkpoint region update, LFS can detect this by seeing inconsistent timestamps
* LFS always chooses to use the most recent checkpoint region with consistent timestamps
* Any segments that were stored in-memory but not saved to disk would be lost on system crash
