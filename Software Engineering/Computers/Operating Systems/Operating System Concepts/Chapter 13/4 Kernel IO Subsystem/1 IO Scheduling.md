# 13.4.1 I/O Scheduling

* Scheduling example using disk
  * Disk arm is near the beginning of a disk
  * Three blocking disk read requests
    * Read block near end of disk
    * Read block near beginning of disk
    * Read block near middle of disk
  * Minimize the distance the disk arm has to travel by executing read requests 2, 3, 1 (in that order)
* Operating systems have a wait queue of requests for each device
* When an application issues a blocking I/O system call, the request is placed on the device's queue
* For asynchronous I/O, the kernel needs to keep track of many I/O requests at a time
  * Attaches wait queue to device-status table
  * Table contains an entry for each device
  * Each table entry indicates the device's type, address, state (idle, busy)
    * A busy device will store the type of request as well as other data about the request
