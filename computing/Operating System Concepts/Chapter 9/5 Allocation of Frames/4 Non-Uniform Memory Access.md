# 9.5.4 Non-Uniform Memory Access

* In systems with many CPUs, there may be multiple system boards, each which may have multiple CPUs
  * These system boards are interconnected through something like a system buss or high-speed network connections
  * CPUs on a particular board can access the momery on that board with less delay than they can access memory on other boards
* Goal is to have memory frames allocated as close as possible to the CPU where the process is running
* Scheduler tracks the last CPU that ran a process
* Scheduler tries to schedule process onto the CPU that last ran that process
* Memory management system tries to allocate frames for the process close to the CPU where the process is scheduled
* A process with many different threads may end up with those threads scheduled on many different system boards
  * The Solaris operating system creates an `lgroup` entity, which groups together "close" CPUs and memory, based on latency
  * Solaris tries to schedule all threads of a process and allocate all memory of a process within an `lgroup`
  * If it's not possible to stay within an `lgroup`, it picks nearby `lgroup`s for the remaining memory / CPU resources
