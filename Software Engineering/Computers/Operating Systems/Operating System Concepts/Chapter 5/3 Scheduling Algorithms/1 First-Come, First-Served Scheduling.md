# 5.3.1 First-Come, First-Served Scheduling

* The process that requested the CPU first is allocated the CPU first
* FIFO queue is used to manage this scheduling strategy
* Average waiting time for FCFS can be long if a long-running process arrives before many short-running processes
  * First process in queue has a run time of 24ms, next two processes in queue have a run time of 3ms -> average waiting time is 17 milliseconds
  * If process order was reversed (3ms, 3ms, 24ms) the average waiting time would be 3ms
  * Average waiting time can vary substantially if process CPU burst time varies
* Scenario with 1 CPU-bound process and many I/O-bound processes
  * CPU-bound process is allocated the CPU
  * All the I/O-bound processes finish I/O and move to ready queue
  * CPU-bound process finishes burst and moves to ready queue
  * All I/O-bound processes have short CPU bursts and finish quickly, moving back to I/O queue
  * CPU-bound process moves back to the ready queue and is allocated the CPU
  * All the I/O-bound processes wait in the ready queue until the CPU-bound process is done
  * If shorter processes were allowed to go first, there might be higher CPU and device utilization
* FCFS is non-preemptive, meaning that a process keeps the CPU until it is terminated or by requesting I/O
* FCFS does not work well with time-sharing systems where it is important that each user gets a share of the CPU at regular intervals 
  * Cannot allow one process to keep the CPU for an extended period of time
