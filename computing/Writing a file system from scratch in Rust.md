# [Writing a file system from scratch in Rust](https://blog.carlosgaldino.com/writing-a-file-system-from-scratch-in-rust.html)

## Structuring the disk

* When data is stored in a hard-disk drive or solid-state drive, it is written in small units called sectors (or pages in SSD)
* The drives don't have any information about what the piece of data represents, it is the job of the file system to interpret the data and provide an abstraction over the storage devices
* File systems divide the disk into fixed-size blocks
  * Most blocks are used to store data written by the end user
  * Some blocks store metadata about the files

### Superblock and bitmaps

* Superblock stores most of the metadata about the file system (block size, timestamps, how many blocks and files are used vs. free)
* Bitmaps track which data blocks and inodes are free
  * An index in the bitmap set to `0` indicates a free slot and an index set to `1` indicates an occupied slot

### Inode

* The inode table stores metadata about files, like permissions, size, location of the underlying data blocks
* The inode table is stored in blocks
* For each slot in the inode bitmap that is set to 1, there will be a corresponding inode in the inode table
  * The index for an inode in the bitmap matches the index in the table

## Pointing to data

* Inode needs to know how to reference the underlying data blocks
* Direct pointers points to a block that has some of the file data
 * With large files, the number of direct pointers is too large
* Indirect pointers store pointers to bloocks that themselves contain direct points to blocks of file data
 * These indirect pointers can also be nested, two or three levels for larger files, where an indirect pointer points to a block of indirect points, each of which point to a block of indirect pointers, etc.
 
## Directories

* inodes can represent both files and directories
* Directories can store a list of files to include in its corresponding data blocks
* Searching for a file can be slow if a directory contains a large number of files
 * The file list should be sorted and traversed via binary search, or represented as a hash table

## Access paths

* When reading from a file, the file system traverses the entire path, visiting each inode along with way (since directories are also represented in the inode table)
* When writing a file, there are five I/O operations
 * Read the bitmap
 * Write to mark the new allocated block (if necessary)
 * Read and write to the inode to update metadata associated with the file
 * Write the data to the block
