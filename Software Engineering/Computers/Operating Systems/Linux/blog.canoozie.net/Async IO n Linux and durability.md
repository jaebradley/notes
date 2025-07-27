# [Async I/O on Linux and durability](https://`/async-i-o-on-linux-and-durability/)
* Traditional I/O interfaces force you to think synchronously
  * System call -> kernel works -> some result
* `io_ring` is composed of two ring buffers (submission queue and completion queue)
  * Can submit dozens of operations with a single `io_uring_submit` system call
* Data sitting in kernel buffers, not yet written to stable storage, will not be durable to a system crash
* Traditional WAL protocol is simple: write the change to a log file, flush the file to disk, apply the chnage to the database
* Idea is to log the "intent to change" separately from the "confirmation of the change"
* Protocol is to
  * Asynchronously write the intent record
  * Perform the operation in memory
  * Asynchronously write the completion record
  * Wait for the completion record to be flushed to disk
  * Return success to the client
* Batch writes into buffers and flush them when they reach 75% capacity
* Completion entries have a pointer back to the corresponding intent entry
* Recovery process involves mapping the logged intent operations to the logged completion operations
  * Only replay operations that have successful completion entries
  * If system crashes when writing an intent record and completion record, the operation is ignored during recovery
* 
