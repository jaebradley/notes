# 9.1 Background

* Instructions being executed must be in physical memory
* Place entire logical address space in physical memory
* Entire program may not be needed, like in the case where custom error handling would not be executed very often
* Being able to execute a program that is only partially in memory be beneficial
  * Programs are no longer constrained by amount of physical memory avaialable, but can be written for much larger _virtual_ address spaces
  * Programs take less physical space, so more programs could be run at the same time
  * Less I/O is needed to load / swap user programs into memory
* Virtual address space of a process is the logical / virtual view of how a process is stored in memory
  * View is that process begins at a certain logical address (`0`) and exists in contiguous (logical) memory even if the underlying physical memory is non-contiguous

```
| stack |
|   |   |
|   V   |
|_______|
| Free  |
|       |
|       |
|       |
|_______|
|       |
|   ^   |
|   |   |
|  heap |
|_______|
|  data |
|_______|
|  code |
|_______|
```

* Heap grows "upward", while stack grows "downward"
  * "hole" between heap and stack will require actual physical pages only if the heap or stack grows
* System libraries can be shared by several processes through mapping of the shared object into a virtual address space
  * Although each process considers the shared libraries to be part of its virtual address space, the actual frames where the libraries reside in physical memory are shared by all the processes
* Virtual memory allows one process to create a region of memory that it can share with another process
