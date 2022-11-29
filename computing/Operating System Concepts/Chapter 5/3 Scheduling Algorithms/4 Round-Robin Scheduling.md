# Round-Robin Scheduling

* Designed for time-sharing systems
* Unit of time is defined called a time quantum / time slice
* Ready queue is treated as a circular queue 
  * CPU scheduler iterates over the ready queue, allocating the CPU to each process for a time interval of 1 time quantum
* CPU scheduler selects first process from FIFO ready queue and sets a timer to interrupt after 1 time quantum
* If process has a CPU burst of less than 1 quantum, the process will release the CPU voluntarily
* If process has a CPU burst longer than 1 quantum, the timer will go off, causing an interrupt to the operating system
  * Context swsitch is executed, and the process is put at the tail of the ready queue
* Same problem with RR scheduling as there is with FCFS - the average waiting time can be quite long
 * Imagine process queue have burst times of 24, 3, and 3 milliseconds and the time quantum duration is 4 milliseconds
 * The second and third processes have to wait for 4 and 7 milliseconds, respectively
* Want the time quantum to be large with respect to the context-switch time (since most of the CPU time should be spent doing work)
 * If time quantum is too large, the scheduling policy becomes FCFS
 * Rule of thumbs: 80% of the CPU bursts should be shorter than the time quantum
