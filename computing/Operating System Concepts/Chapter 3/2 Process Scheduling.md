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


