# 10.3.6 Acyclic-Graph Directories
* Acyclic graphs allow directories to share subdirectories and files
* Means that the same file or subdirectory may be in two different directories
* Shared file / directory is not the same as two copies of a file
 * Only one file exists, so any changes are immediately visible to the other
* Common way of implementing shared files and subdirectories is by creating a directory entry called a link
  * Link is a pointer to another file or subdirectory
  * If directory entry is marked as a link, the link is resolved using the underlying file's path name
  * Operating system ignores these links when traversing directory trees to preserve the acyclic structure of the system
* In a system where sharing is implemented by symbolic links, dealing with file deletion is easier
 * The deletion of a link does not affect the original file, only the link is removed
 * If the underlying file entry is deleted, the space for the file is deallocated and the link is "dnagling"
 * When the link is accessed and the file associated with the link is not found, the access can be treated like an illegal file name
 * In UNIX, symbolic links are left when a file is deleted - it is up to the user to realize that the original file is gone
* Can approach file deletion by keeping a reference to the number of references to the original file
 * Only when the reference count is 0 can a file be deleted
 * UNIX keeps a reference count in the file information block (inode) for nonsymbolic ("hard") links
 * By prohibiting multiple references to directories, the acyclic graph structure is maintained
