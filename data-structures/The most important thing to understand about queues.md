# [The most important thing to understand about queues](https://blog.danslimmon.com/2016/08/26/the-most-important-thing-to-understand-about-queues/)

* As you approach maximum throughput, average queue size - and therefore average wait time - approaches infinity
  * Applies to unbounded queues
* When a queueing system is at 100% utilization the queue will grow to infinity over a sufficiently long time
  * If the processor is always busy, there's never an instant when a newly arriving task can be assigned immediately to the processor
  * So there's always a task waiting in the queue
  * Proof by induction goes something like this
    * When 100% utilization then queue and processor can't be empty
    * Processor can't have a single item and an empty queue (or else back to previous state)
    * Processor can't be processing a single item and have a queue with a single element or else once the item is done, back to previous state
    * Can't have two items in queue and busy processor because once processor is done back to previous state
    * etc
* Average capacity utilization is 100% = queue size is growing without bound
  * Since average service time is directly proportional to queue size, this means that queue at 100% average capacity utilization will have wait times that grow without bound
* Simulation using single queue that can process 1 task per second, and tested a variety of different arrival rates to get different average capacity usage rates
  * When reached 96% utilization, average wait times were 20 seconds
* Solutions are to increase capacity, decrease demand, or set an upper bound for queue size
  * Benefit of third approch is that it forces acknowledgement that queue size is always finite and to think through failure modes when queue is full
