# 9.9.6 I/O Interlock

* I/O is often implemented by a separate I/O processor
  * A controller for a USB storage device is generally given the number of bytes to transfer and a memory address for the buffer
  * When the transfer is complete, the CPU is interrupted
* Need to ensure that the following situation does not happen
  * Process issues I/O request and is put in a queue for that I/O device
  * Other processes use CPU while the original process sits in the I/O device's queue
  * These processes cause page faults
  * One page fault, using a global replacement algorithm, replaces the page containing the memory buffer for the waiting process
  * When the I/O request for the original process is now at the head of the device queue, the device now references a frame address that is being used by another process
* To ensure that the previous solution does not happen
  * Never execute I/O to user memory
    * Data is copied between system memory and user memory
    * I/O only occurs between system memory and the I/O device
    * For writes, copy the block of data to system memory and then write the block of data to tape (this will incur extra overhead)
  * Allow pages to be locked in memory
    * A lock bit is associated with every frame
    * If a frame is locked, it cannot be selected for replacement
    * For writes, the pages containing the block of data are locked
      * When the data is written to disk, the pages are unlocked
