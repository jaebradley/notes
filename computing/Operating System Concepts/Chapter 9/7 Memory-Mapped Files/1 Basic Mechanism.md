# 9.7.1 Basic Mechanism

* Map a disk block to a page / pages in memory
* Initially accessing the file leads to ordinary demand paging, which results in a page fault
* Page-sized portion of the file is read into physical memory
  * Subsequent reads and writes on the file are handled as routine memory accesses vs. incurring cost of system calls like `read` and `write`
  * Since these system calls would normally involve disk I/O, accessing files in memory is much faster
