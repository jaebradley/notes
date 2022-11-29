# Round-Robin Scheduling

* Designed for time-sharing systems
* Unit of time is defined called a time quantum / time slice
* Ready queue is treated as a circular queue 
  * CPU scheduler iterates over the ready queue, allocating the CPU to each process for a time interval of 1 time quantum
* CPU scheduler selects first process from FIFO ready queue and sets a timer to interrupt after 1 time quantum
* If process has a CPU burst of less than 1 quantum, the process will release the CPU voluntarily
* If process has a CPU burst longer than 1 quantum, the timer will go off, causing an interrupt to the operating system
  * Context swsitch is executed, and the process is put at the tail of the ready queue
