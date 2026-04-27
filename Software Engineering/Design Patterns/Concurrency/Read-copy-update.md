# [Read-copy-update](https://en.wikipedia.org/wiki/Read-copy-update)

* Synchronization mechanism that avoids the use of lock primitives while multiple threads concurrently read and update elements that are linked through pointers and that belong to shared data structures
* Whenever a thread is inserting or deleting elements of data structures in shared memory, all readers are guaranteed to see and traverse either the older or the new structure, therefore avoiding inconsistencies
* Used when performance of reads is crucial - makes all readers proceed as if there were no synchronization, but makes updates more difficult

## Name and overview

* Create a new structure
* Copy the data from the old structure into the new one, and save a pointer to the old structure
* Modify the new, copied structure
* Update the global pointer to refer to the new structure
* Sleep until the operating system kernel determines that there are no readers left using the old structure - `synchronize_rcu()` in the Linux kernel
* Once awakened by the kernel, deallocate the old structure

## Detailed description

* A key property of RCU is that readers can access a data structure even when it is in the process of being updated

### Global Pointer Example

* A global pointer that is initially null that might be accessed by a reader at any time
* A new struct is created that is not accessible to readers
* Assigning a reference to the struct to the global pointer
* Now the struct is accessible to readers
* Concurrent readers will either see the previous null pointer or a valid pointer to the new struct

### Example of how to delete an element from a linked list

* Linked list with A -> B -> C
* Readers may be referencing any of the elements at any given time
* Node B needs to be deleted
* So first, Node A is changed to point to Node C
  * Readers of Node A will either obtain a reference to Node B or Node C during this operation
  * After Node A refers to Node C, only "pre-existing" readers might still have a reference to Node B - "new" readers will have no way to get a reference to Node B
* There is a wait-for-readers operation (which, again, only needs to wait for "pre-existing" readers) that waits until Node B is no longer referenced
* Once Node B is no longer referenced, it is safe to free Node B
* When Node A is being changed to reference Node C instead of Node B, there are two different "versions" of the list where Node A points to Node B and Node A points to Node C
  * This is called "coordination in space"
* More traditional synchronization methods like locking or transactions do not coordinate in space
* Most common non-blocking algorithm for a shared data structure
* RCU is completely wait-free for any number of readers
* Single-writer implementations (like using a single thread to write) is also lock-free for the writer
