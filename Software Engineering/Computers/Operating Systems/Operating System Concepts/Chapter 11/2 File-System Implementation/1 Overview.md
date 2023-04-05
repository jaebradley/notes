# 11.2.1 Overview

## On Disk Structures

* Boot control block
  * This per-volume block can contain information needed by the system to boot an operating system from that volume
  * If the disk does not contain an operating system, this block can be empty
  * Typically the first block of a volume
* Volume control block
  * This per-volume block contains volume / partition details
  * # of blocks in partition, size of blocks, free-block count
  * Per-file file-control block contains file metadata (like unique file identifier)

## In-Memory Structures

* Structures are created / loaded at mount time and discarded at dismount
* In-memory mount table, containing information about each mounteded volume
* In-memory directory-structure cache holds directory information of recently-accessed directories
* System-wide open-file table contains a copy of the file-control block of each open file
* Per-process open-file table contains a pointer to the appropriate entry in the system-wide open-file table
* Buffers hold file-system blocks when they are being read / written from / to disk

## File Creation Example

* Application program calls the logical file system
* Logical file system knows the directory structure
* Logical file system allocates a new file-control block
* Logical file system reads the appropriate directory into memory, with the created file's name, and writes the file-control block back to disk
* Some operating systems like UNIX treat a directory the same as a file (type is "directory")
* Logical file system calls the file-organization module to map the directory I/O into disk-block numbers
  * Disk-block numbers are passed on to the basic file system and I/O control system

## Opening a File

* File is created, so I/O can now happen
* File must be opened
* `open` system call searches the system-wide open-file table to see if the file is already in use by another process
* If the file is already used by another process, an entry is created in the per-process open-file table pointing to the existing system-wide open-file table
* If the file is not already used by another process, directory structure is searched for the file
  * If file is found, the file-control block is copied into the system-wide open-file table (in-memory)
  * System-wide open-file table tracks the number of processes that have the file open
* Entry made in per-process open-file table with a pointer to the file's entry in the system-wide open-file table
  * The pointer to current location in file is stored
  * File access mode is stored
* `open` system call returns the appropriate entry in the per-process open-file table
* All file operations are then performed via this pointer
  * UNIX calls this pointer a file descriptor while Windows calls the pointer a file handle

## Closing a File

* When a process closes the file, the per-process table entry is removed
* System-wide entry's open count is decremented
* When all processes close the file, any updated metadata is copied back to the disk-based directory structure and the system-wide open-file table entry is removed
* Most systems keep all information about an open file, except the actual file data, in memory
