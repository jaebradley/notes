# Chapter 9. The `multiprocessing` Module

* Python threads are OS-native, and _not_ simulated
  * Bound by Global Interpreter Lock, so only one thread interacts with Python objects at any given time
* By executing many processes at the same time, a number of Python interpreters are run in parallel, each with a private memory space with its own GIL

## Estimating Pi Using Processes and Threads

* No speedup when using more threads due to GIL and the fact that the task is CPU-intensive
  * Although each thread could run on a separate CPU, it will only execute when no other threads are running
* Linear speedup with more processes
  * Maximum speedup when number of processes = number of physical cores even if the number of processes can be larger than the number of physical cores due to Hyper-Threading
  * Each forked process has a private Python interpreter running on a single thread
  * No GIL contention across these forked processes as no objects are shared
* Spawning threads is much more lightweight than forking a process
* Expect a forked process to take on 10-20 MB of RAM
  * Increasing the installed libraries and data, each forked copy may end up using hundreds of MB
  * If the system reverts to using disk swap space after it runs out of RAM, any parallelization will be lost to paging RAM back and forth to disk
