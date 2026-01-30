# 5.3 Blocking queues and the producer-consumer pattern

* If the queue is full, `put` blocks until space becomes available
* If the queue is empty, `take` blocks utnil an element is available
* Unbounded queues are never full, so a `put` on an unbounded queue never blocks
* Dish washing analogy - person washing dishes and placing them on drying rack (the queue) and a person drying the dishes, taking them off the drying rack
  * The dish rack is the blocking queue
  * If there are no dishes in the rack, the consumer waits until there are dishes to dry and if the rack fills up, the producer has to stop washing until there is more space
  * There can be mulitple washers (producers) and multiple consumers (dryers) - each worker only interacts with the dish rack (queue)
* If the producers consistently generate work faster than the consumers can process it, eventually the application will run out of memory because work items will queue up without bound
  * If a bounded queue is used, when the queue fills up, the producers block, giving consumers time to catch up because a blocked producer cannot producer more work
* Build resource management into your design early using blocking queues - it is a lot easier to do this up front than to retrofit it later
* `SynchronousQueue` maintains a list of queued threads waiting to enqueue or dequeue an element
  * Would be like having no dish rack, but instead handing the washed dishes directly to the next available dryer
  * Reduces the latency associated with moving data from producer to consumer because the work can be handed off directly (in a traditional queue, the enqueue and dequeue operations must be completed sequentially before a unit of work can be handed off)
  * Generally only suitable when there are enough consumers that there nearly always will be one ready to take the handoff

## 5.3.3 Deques and work stealing

* A Deque is a double-ended queue that allows efficient insertion and removal from both the head and the tail
* In a work-stealing design, every consumer has its own deque
  * If a consumer exhausts the work in its own deque it can steal work from the tail of someone else's deque
  * Work-stealing can be more scalable than a traditional producer-consumer design because workers don't contend for a shared work queue, most of the time they access only their own deque
  * When a worker has to access another's queue, it does so from the tail rather than the head, further reducing contention
* When a worker identifies a new unit of work, it places it at the end of its own deque and when it's deque is empty it looks for work at the end of someone else's deque, ensuring that each worker stays busy
