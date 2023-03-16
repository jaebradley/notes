# 5.5.1 Approaches to Multiple-Processor Scheduling

* Have all scheduling decisions, I/O processing, etc handled by a single main processor (asymmetric multiprocessing)
  * Other processors execute only user code
  * Simpler as only one processor accesses the system data structures
* Each processor is self-scheduling (symmetric multiprocessing / SMP)
  * Processors examine a ready queue (could be a common ready queue or each processor has their own ready queue)
  * Ensure two processors do not choose the same process and that processes are not lost from the queue
