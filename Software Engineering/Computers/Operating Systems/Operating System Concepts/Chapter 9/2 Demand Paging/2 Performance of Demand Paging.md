# 9.2.2 Performance of Demand paging

* Memory access time ranges from 10 to 200 nanoseconds
* If `p` is the probability of a page fault, then the effective memory access time is
  * `((1 - p) x memory access time) + (p x page fault time)`
* Page fault causes the following sequence
  * Trap to the operating system
  * Save user registers and process state
  * Check that interrupt was due to page fault
  * Check if page reference was legal for the process, and if so, identify location of the page on the disk
  * Issue a read from the disk to a free frame
    *  Wait in a queue for this device until the read request is started
    *  Wait for the device seek
    *  Begin the transfer of the page to a free frame
  *  While waiting for the read to complete, allocate the CPU to some other user
  *  Receive an interrupt from the disk I/O
  *  Save the registers and process state for the other user process, if the CPU has been allocated to some other user process
  *  Verify that interrupt was due to disk I/O
  *  Correct the page table (and any other tables) to show that the desired page is now in memory
  *  Wait for the CPU to be allocated to the process again
  *  Restore the previous state (user registers, process state, updated page table) and resume the interrupted instruction
*  If the average page-fault service time is 8 milliseconds (typical hard disk has an average latency of 3 milliseconds, and a seek time of 5 milliseconds)
  *  One out of 1,000 accesses causes a page fault, then the effective access time is ~8 microseconds, and the computer is slowed down by a factor of 40 because of demand paging
  *  If there is a performance degradation of less than 10%, must allow fewer than one memory access out of ~400k to result in a page-fault
