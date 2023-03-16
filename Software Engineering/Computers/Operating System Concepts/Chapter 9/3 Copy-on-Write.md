# 9.3 Copy-on-Write

* Process creation using the `fork` system call creates a child process that is a duplicate of its parent
* Traditionally, `fork` works by making a copy of the parent's address space for the child, duplicating the parent's pages
* Since many child processes invoke the `exec` system call immediately after creation, copying of the parent's address space may be unnecessary
* Instead, the parent and the child processes initially share the same pages, which are marked as copy-on-write pages
  * If a modification is made to a shared copy-on-write page by either parent or child process, a copy of the shared page is created
* For example, if the child process attempts to modify a page containing portions of the stack
  * The operating system will create a copy of the page, mapped to the address space of the child process
  * Child process will modify its copied page and not the page belonging to the parent process
* Many operating systems have a pool of free pages (in case the stack or heap for a process must expnd)
