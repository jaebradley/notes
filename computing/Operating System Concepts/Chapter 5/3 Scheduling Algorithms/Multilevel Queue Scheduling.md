# Multilevel Queue Scheduling

* The ready queue gets partitioned into several separate queues
  * These queues are based on a property of the proocess (like memory size, process priority, process type)
  * Each queue has its own scheduling algorithm
    * Foreground queue might be scheduled using a round-robin algorithm while the background queue might be scheduled using a FCFS algorithm
* There is scheduling among the queues, commonly implemented as a fixed-priority preemptive scheduling (foreground queue may have absolute priority over the background queue)
  * So no processes in the background queue would execute until the foreground queue was empty
