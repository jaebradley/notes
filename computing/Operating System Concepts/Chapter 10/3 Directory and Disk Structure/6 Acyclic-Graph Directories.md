# 10.3.6 Acyclic-Graph Directories

* Trees prohibit the sharing of files or directories
* Acyclic graphs allow directories to share subdirectories and files
* Shared file / directory is not the same as two copies of a file
* Common way of implementing shared files and subdirectories is by creating a directory entry called a link
  * Link is a pointer to another file or subdirectory
  * If directory entry is marked as a link, the link is resolved using the underlying file's path name
  * Operating system ignores these links when traversing directory trees to preserve the acyclic structure of the system
* Another common approach to implementing shared files
