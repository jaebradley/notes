# 9.7.1 Basic Mechanism

* Map a disk block to a page / pages in memory
* Initially accessing the file leads to ordinary demand paging, which results in a page fault
* Page-sized portion of the file is read into physical memory
  * Subsequent reads and writes on the file are handled as routine memory accesses vs. incurring cost of system calls like `read` and `write`
  * Since these system calls would normally involve disk I/O, accessing files in memory is much faster
* Writes to the file stored in memory do not lead to synchronous writes to the file on disk
 * Some systems may choose to update the physical file when the operating system periodically checks whether the page in memory has been modified
 * When the file is closed, all the memory-mapped data is written back to disk and removed from the virtual memory of the process
* Some operating systems provide memory-mapping through only specific system calls while other operating systems memory-map files by default
* Multiple processes may be allowed to map the same file concurrently
 * Writes by any of the processes modify the data in virtual memory and can be seen by all other processes that map the same section of the file
 * The virtual memory map of each sharing process points to the same page of physical memory
 * This page in physical memory is the page that holds a copy of the file disk block
* UNIX and Linux use the `mmap` system call
