# 3.3 Operations on Processes

## 3.3.1 Process Creation

* When a process creates a subprocess, that subprocess may be able to obtain its resources directly from the operating system, or it may be constrained to a subset of the resources of the parent process
* The parent may have to partition its resources among its children or it may be able to share some resources among several of its children
  * Restricting a child process to a subset of the parent's resources prevents any process from overloading the system by creating too many subprocesses
* 2 possibilities when a process creates a new process
  * The parent continues to execute concurrently with its children
  * The parent waits until some or all of its children have terminated
* 2 possibilities for the address space of the new process
  * The child process is a duplicate of the parent process - same program and same data
  * Child process has a new program loaded into it
* In Unix, the `fork` system call creates a new process
  * The new process consists of a copy of the address space of the original process
  * Both parent and child processes continue execution after the `fork`
  * The `exec` system call is used after a `fork` to replace the process' memory space with a new program
    * `exec` loads a binary file into memory and starts its execution
  * If parent has nothing left to do while child executes, it can issue a `wait` system call to move itself off the ready queue until the termination of the child

## 3.3.2 Process Termination

* Executes final statement and calls the `exit` system call, which tells the operatin gsystem to delete it
* Process returns a status value (an integer) to its parent process (`wait` system call)
  * Process resources (physical / virtual memory, open files, I/O buffers) are deallocated by the operating system
* A parent may terminate the execution of one of its children due to
  * The child exceeding some resource that has been allocated to it
  * The task assigned to the child is no longer required
  * The parent is exiting and the operating system does not allow a child to continue if its parent terminates
