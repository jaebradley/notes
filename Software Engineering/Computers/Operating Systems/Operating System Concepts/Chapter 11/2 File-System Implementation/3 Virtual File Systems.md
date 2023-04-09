# 11.2.3 Virtual File Systems

* File-system implementation consists of three layers
  * First layer is the file-system interface
  * Second layer is the virtual file system layer

## Virtual File System

* Several implementations for the VFS interface may coexist on the same machine
  * Allows for transparent access to different types of file systems mounted locally
* Mechanism for uniquely representing a file throughout a network
  * VFS is based on a file-representation structure (`vnode`)
  * `vnode`s have a numerical identifier for network-wide unique files
  * UNIX `inode`s are unique within a single file system

## VFS Architecture in Linux

* `inode` object - represents an individual file
* `file` object - represents an open file
* `superblock` object - represents an entier file system
* `dentry` object - represents an individual directory entry
* VFS defines a set of operations that need to be implemented for each of these objects 
* Every object contains a pointer to a function table
  * Function table lists the addresses of the actual functions that implement the defined operations for that particular object
  * Example of some of the functions that the `file` object implements
    * `int open(...)` - open a file
    * `ssize_t read(...)` - read from a file
* Implementation of the file object for a specific file type is required to implement each function specified in the definition of the file object
* VFS calls the function from an object's function table without having to know in advance what kind of object it is dealing with
  * VFS does not know / care if an inode represents a disk file, a directory file, or a remote file
  * The appropriate function for that file's `read` operation will always be at the same place in its function table, and the VFS software layer will call that function without caring how the data is actually read

