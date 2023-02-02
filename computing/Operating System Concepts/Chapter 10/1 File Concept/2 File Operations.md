# 10.1.2 File Operations

* Operating system provides system calls to create, write, read, reposition, delete, and truncate files
* Create
  * Space in the file system must be found for the file
  * Entry for the file must be made in the directory
* Write
  * System call specifying the file name and the data to write to the file
  * A write pointer is specified for each file that specifies the location in the file where the next write is to take place
* Read
  * System call that specifies the name of the file and the in-memory location where the next block of the file should be put
  * Directory is searched for the associated entry
  * A read pointer is kept for the location in the file where the next read should take place
  * Once the read has taken place, the read pointer is updated
  * Per-process pointer for a position in a file
* Delete
 * Search directory for the file
 * Release all file space, and erase directory entry
* Truncating a file
 * Erase contents but keep attributes
 * All attributes renamin unchanged, other than file length
 * File space is released
* To avoid constantly searching the directory for the file, many systems require an `open` system call before the file is used
 * Operating system keeps a small table (open-file table) containing information about all open files
 * When a file operation is requested, the file is specified via an index into this table, eliminating searching
 * When the file is no longer being actively used, it is closed by the process, and the file's entry in the open-file table is removed
* If several processes may open the same file at the same time, operating system uses two different internal tables
 * A per-process table and a system-wide table
 * Per-process table has process-specific information like the current file pointer for each file
 * System-wide table contains process-independent information, like the location of the file on disk
 * Each per-process table entry points to the system-wide open file table
