# 5.3.2 Shortest-Job-First Scheduling

* Each process is associated with the length of the process's next CPU burst
* When the CPU is available, it is assigned to the process with the smallest next CPU burst
* If there are four processes with burst times of 6, 8, 7, and 3 milliseconds, the average waiting time is 7 milliseconds using the SJF scheduling algorithm
  * If the FCFS scheduling algorithm was used, the average waiting time would be 10.25 milliseconds
* SJF is provably optimal, minimizing the average waiting time
* SJF scheduling is used frequently in long-term scheduling
  * The next CPU burst time could be calculated from the user-specified process time limit, when submitting the process
  * If too low a value is submitted, there will be a time-limit-exceeded error that will require resubmission
* For short-term scheduling, harder to calculate next CPU burst length
  * Can approximate using length of previous CPU burst
* A pre-emptive SJF algorithm will preempt the currently executing process if a newly arriving process to the ready queue has a next CPU burst that is shorter than what is left of the currently executing process
