# 9.6.1 Cause of Thrashing

## When Could Thrashing Occur

* Imagine a process that does not have enough frames to support the pages in active use, a page fault will occur
* The process will need to replace a page
* However, since all pages are in active use, it must replace a page that will be needed again right away
* The replaced page is needed again, and results in a page fault
* Thrashing is the name for this cycle of replacing pages that must be brought back into memory right away

## Performance Problems

* Operating system monitors CPU utilization
* When CPU utilization is low, the operating system increases the degree of multiprogramming by introducing a new process to execute
* If some process needs more frames, it starts page faulting and taking frames away from other processes
* Those other processes need the frames that have been replaced and start page faulting, as well
* As the page faults increase, and processes wait for the paging device, CPU utilization decreases
* The CPU scheduler sees a decrease in CPU utilization and increases the degree of multiprogramming
* New process starts and needs frames, so takes frames from a running process, which causes more page faults, and longer queues from the paging device, which causes CPU utilization to drop even more
* Limit the effects of thrasing by using a local replacement algorithm
  * If a process starts thrashing, it cannot steal frames from another process and cause that process to thrash too
  * A thrashing process will increase the queue for the paging device
  * Average service time for a page fault will increase due to this longer than average paging device queue
  * Thus, the effective access time for all the non-thrashing processes will also increase

## Locality Model

* As a process executes, it moves from "locality" to "locality"
* A "locality" is a set of pages that are actively used together
* For example, when a function is called, it defines a new "locality"
  * There are local variables, and when the function is exited, the "locality" is no longer needed since the local variables are no longer in active use
* If a process is allocated enough frames for its current locality, it will fault for all pages in the locality, and then not fault again until it changes locality
  * If a process is not allocated enough frames for its current locality, it will thrash since it cannot keep in physical memory all the pages that it is actively using
