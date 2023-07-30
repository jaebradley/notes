# 13.4.7 Kernel Data Structures

* While UNIX supports `read` operations to a variety of entities (user files, raw devies) the semantics differ
  * For reading a user file, the kernel needs to probe the buffer cache before deciding whether to perform disk I/O
  * To read a raw disk, the kernel needs to ensure that the request size is a multikple of the disk sector size and is aligned on a sector boundary
* Open file table contains records with pointers to the appropriate read/write/select/close functions
