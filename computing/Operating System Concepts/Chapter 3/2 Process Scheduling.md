# 3.2 Process Scheduling

## 3.2.1 Scheduling Queues

* As process enter the system, they are put into a job queue, which consists of all the processes in the system
  * This queue is usually stored as a linked list
  * The ready queue points to the first and final Process Control Blocks
    * The ready queue contains to all the processes that are ready and waiting to execute
    * The ready queue is stored in main memory
  * Each Process Control Block points to the next Process Control Block in the ready queue
* Device queues exist when processes wait for a device to complete requests (like an I/O request)
  * If a process make an I/O request to a shared device, such as a disk, the disk may be busy with the I/O request of another process
  * The process must wait until the disk is ready to process it's request
* Once a process is executing, it could
  * issue an I/O request and then be placed in an I/O queue
  * create a new subprocess and wait for the subprocess' termination
  * be forcibly removed from the CPU, as a result of an interrupt, and be put back in the ready queue
* When a process terminates, it is removed from all queues, and has its Process Control Block and other resources deallocated

## 3.2.2 Schedulers

* A process migrates among the various scheduling queues throughout its lifetime
  * The operating system selects processes from these queues for scheduling purposes
* Often times, more processes are submitted than can be executed
  * These processes are spooled to a mass-storage device, like a disk, where they are kept for later execution
  * The long term scheduler (or job scheduler) selects processes from this pool and loads them into memory for execution
  * The short term scheduler (or CPU scheduler) selects processes from ready queue and allocates CPU to one of them
* Short-term scheduler must select a new process for the CPU frequently
  * Processes may only execute for a few milliseconds before waiting for I/O
  * Time spent figuring out which process to execute next is time wasted not computing / executing that process
* It is important that they long-term scheduler selects a good mix of I/O and CPU-bound processes
  * If all processes are I/O-bound, then the ready queue will almost always be empty and the short-term scheduler will have nothing to do
  * If all processes are CPU-bound, then the I/O waiting queues will almost always be empty, devices will go unused
* Time-sharing systems like Unix do not have a long-term scheduler and put every new process in memory for the short-term scheduler
* Medium-term schedulers are used to remove processes from memory (and thus, from active contention for the CPU)
  * This reduces the degree of multiprogramming (the number of processes in memory)
  * Process can be reintroduced into memory later, and execution can continue where it left off (this is called "swapping)

## 3.2.3 Context Switch

* Interrupts cause the operating system to change a CPU from its current task and run a kernel routine
* When an interrupt happens, the system saves the current context of the process running on the CPU so that it can restore the context when its processing is done
  * Effectively suspending the process and resuming it later
* This state save of the current process and the state restore of another process is called a context switch
* Context switch times (usually a few milliseconds) are pure overhead as no useful work is done while switching
