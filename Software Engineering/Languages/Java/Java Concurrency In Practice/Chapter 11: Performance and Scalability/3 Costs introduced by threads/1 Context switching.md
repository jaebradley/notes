# 11.3.1

* If there are more runnable threads than CPUs, eventually the OS will preempt one thread so that another can use the CPU
* Context switching requires saving the execution context of the currently running thread and restoring the execution context of the newly scheduled thread
  * Can take ~5-10k clock cycles (a few microseconds)
* A new execution context generally means new / different data, which means cache misses
* When a thread blocks because it is waiting for a contended lock, the JVM usually suspends the thread and allows it to be switched out
