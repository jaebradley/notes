# 4.1 Overview

## 4.1.1 Motivation

* A thread is a basic unit of CPU utilization, it comprises a thread ID, a program counter, a register set, and a stack
  * Shares code section, data section, operating-system resources (open files, signals) with other threads belonging to the same process
* Examples: web processor has one thread for displaying graphics, another thread for responding to keystrokes, and a third for performing spelling and grammar checking
* Can have web server run as a single process accepting requests
 * When a server receives a request, it creates a separate process to service that request
 * Process creation is time consuming and resource intensive - more efficient to have a single process with many threads
* Most operating system kernels are multithreaded, with each thread performing a specific task
 * For example, Linux uses a kernel thread for managing the amount of free memory in the system

## 4.1.2 Benefits

* Processes may only share resources through shared memory or message passing
 * Threads share the memory and resources of the process to which they belong to
 * So several threads of activity within the same address space
* In Solaris, creating a process is about 30x slower than creating a thread, and context switching is about 5x slower
* Single-threaded process can only run on one processor, regardless if there are multiple processors available
 * Multithreading allows threads to be running in parallel on different processors

## 4.1.3 Multicore Programming

* Multiple computing cores on a single chip, where each core appears as a separate processor to the operating system
* On a system with a single computing core, having multiple threads means that the execution of the threads will be interleaved over time as the processor is only able to execute one thread at a time
 * On a system with multiple computing cores, threads can execute in parallel, and the system can assign separate threads to each core
