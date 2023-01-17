# 4.5 Operating-System Examples

## 4.5.2 Linux Threads

* Linux provides the ability to create threads using the `clone()` system call
* `clone()` can be passed flags such that the parent and child tasks will share the same file-system information, memory space, signal handlers, set of open files
  * Parent task shares most of its resources with its child task
  * If none of the flags are set, no sharing takes place, similar functionality similar to `fork()` system call
* A unique kernel data structure exists for each task in the system
  * Contains pointers to other data structures where data is stored
    * Data structures representing the list of open files, signal-handling information, and virtual memory
  * When `fork()` is called, a new task is created, along with a copy of all the associated data structures of the parent process
  * A new task is also created when the `clone()` system call is made
    * Instead of copying all data structures, the task points to the data structures of the parent task (depending on the flags passed to `clone()`)
