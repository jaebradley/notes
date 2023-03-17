# 8.2 Swapping

* A process can be swapped temporarily out of memory into a backing store, commonly a fast disk
* Ideally, memory manager can swap processes fast enough so that there is some process in memory when the CPU scheduler wants to reschedule the CPU
* The allocated CPU time slice must be large enough to allow reasonable amounts of computation between swaps
* Normally, a process that is swapped out will be swapped back into the same memory space it occupied previously
  * This is true for assembly or load time binding
  * Process can be swapped into a different memory space for execution-time binding
* A 100 MB process on a hard disk with a transfer rate of 50 MB per second means that to/from main memory takes 2 seconds
  * If latency ~8ms, total swap in and swap out is ~4016ms
  * Note that swap time is mostly due to the size of the process

## I/O

* Only swap processes that are completely idle
* If a process is waiting for an I/O operation and the I/O is asychronously accessing the user memory for I/O buffers, then the process cannot be swapped as the I/O operation might attempt to use memory belonging to the newly swapped process
* Either never swap a process with pending I/O or execute I/O operations only into operating-system buffers
  * Transfers between operating-system buffers and process memory then occur only when the process is swapped in
