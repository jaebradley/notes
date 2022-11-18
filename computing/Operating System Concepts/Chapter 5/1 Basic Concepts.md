# 5.1 Basic Concepts

* A process is executed until it must wait (completion of some I/O request), CPU sits idle
* Multiprogramming uses this waiting time productively by keeping many processes in memory
  * When one process waits, the operating system takes the CPU away from that process and gives the CPU to another process

## 5.1.1 CPU-I/O Burst Cycle

* Processes alternate between bursts of CPU activity and I/O activity
* CPU bursts generally have a large number of short CPU bursts and a small number of long CPU bursts
  * I/O bound programs typically have many short CPU bursts
  * CPU-bound programs may have a few long CPU bursts

## 5.1.2 CPU Scheduler

* When the CPU is idle, the operating system (short-term scheduler/CPU scheduler) selects a process in the ready queue for execution
* Ready queue is not necessarily a FIFO queue - it can be a priority queue, a tree, an unordered linked list
* Ready queue comprised of the process control blocks

## 5.1.3 Preemptive Scheduling

* CPU scheduling may occur when
  * Process switches from running state to waiting state (I/O request, or invocation of `wait` for the termination of a child process)
  * Process switches from running state to ready state (an interrupt occurs)
  * Process switches from waiting state to ready state (completion of I/O)
  * Process terminates
* For the first and last situations (process goes from running to waiting, or when a process terminates), a new process will be scheduled for execution (if one exists in the ready queue)
  * If scheduling **only** occurs in these two situations, called nonpreemptive, or cooperative, scheduling scheme
  * If scheduling occurs in other situations, the scheduling scheme is called preemptive
* Preemptive scheduling has shared data costs associated with it
  * Two processes share data
  * First process updates the data
  * While updating the data it is preempted, so that the second process can run
  * The second process tries to read the data, which is now in an inconsistent state
  * In these situations, new mechanisms are necessary to coordinate access to shared data
* Preemptive scheduling also affects operating-system kernel design
  * Most versions of UNIX wait for a system call to complete or for an I/O block to take place if the kernel is busy on behalf of a process before context switching
* Interrupts can occur at any time, and they cannot always be ignored by the kernel
  * Sections of code affected by interrupts must be guarded from simulatenous use
  * These sections of code avoid concurrent access by several processes by disabling interrupts at entry and reenabling interrupts at exit

## 5.1.4 Dispatcher

* The dispatcher is a component that gives control of the CPU to the process selected by the short-term scheduler
* This component switches contexts, switches to user mode, and jumps to the proper location in the user program to continue execution
* Needs to be quick, as it is invoked during every context switch
