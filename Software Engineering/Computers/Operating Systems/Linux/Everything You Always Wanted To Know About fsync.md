# [Everything You Always Wanted to Know About `fsync`](https://blog.httrack.com/blog/2013/11/15/everything-you-always-wanted-to-know-about-fsync/)

* fsync is intended to force a physical write of data from the buffer cache
  * assures that after a system crash or other failure, all data up to the time of the fsync call is recorded on disk
* foo/bar.txt = access the data blob associated with foo, find the entry for bar.txt, and open bar.txt’s associated data blob
* Add foo/baz.txt = access the foo data blob, write the updated data blob, write to the newly allocated baz.txt blob.   * Writes two data structures - the directory entry and the file
* fsync guarantees file-specific durability, but does not mention the directory entry itself
* on POSIX systems you can have an arbitrary number of directory links to a filename
* In theory, file creation, written data, synchronize file, close file, and see the file lost due to power failure
* Filesystem check may find the directory entry partially written and achieve consistency by taking the previous directory blob, wiping the now unreferenced file entry
* Have to actually make sure your filename entry is properly sync’d on disk following the POSIX spec
* Need to call fsync twice - once on the file data, and once on the parent directory to achieve durability
