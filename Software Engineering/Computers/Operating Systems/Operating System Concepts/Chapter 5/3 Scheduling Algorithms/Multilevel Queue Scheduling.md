# Multilevel Queue Scheduling

* The ready queue gets partitioned into several separate queues
  * These queues are based on a property of the proocess (like memory size, process priority, process type)
  * Each queue has its own scheduling algorithm
    * Foreground queue might be scheduled using a round-robin algorithm while the background queue might be scheduled using a FCFS algorithm
* There is scheduling among the queues, commonly implemented as a fixed-priority preemptive scheduling (foreground queue may have absolute priority over the background queue)
  * So no processes in the background queue would execute until the foreground queue was empty
  * If a foreground process entered the foreground ready queue while a background process was executing, the background process would be preempted
  * Can also time-slice amongst queues, where each queue is given a certain percentage of CPU time
  * For example, the foreground queue could be given 80% of CPU time, while the background queue receives 20% of the CPU time
