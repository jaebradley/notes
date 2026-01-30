# 10.3 Other liveness hazards

## 10.3.1 Starvation

* Thread API defines ten priority levels that the JVM maps to operating system scheduling priorities
  * Mapping is platform-specfic, so two Java priorities can map to the same OS priority on one system and different OS priorities on another system
* Avoid the temptation to use thread priorities since they increase platform dependence and can cause liveness problems
  * Most concurrent applications can use the default priority for all threads

## 10.3.2 Poor responsiveness

* CPU-intensive background tasks can still affect responsiveness because they can compete for CPU cycles with the event thread
* One case where adjusting thread priorities makes sense - when compute-intensive background tasks impact responsiveness

## Livelock

* Livelock occurs when a thread is not blocked, but cannot make progress
* Examples occur in transactional messaging applications where a message is rolled back if the message cannot be processed successfully
  * Message is put back at the head of the queue
  * Message handler is called over and over again every time the message is dequeued
  * So message handling thread never makes progress
* Solution is to introduce some randomness in the retry mechanism
* Example involving two ethernet stations
  * Stations send packet at the same time, and packets collide
  * If stations retry exactly one second later, the packets will collide again (and again and again)
  * Wait an amount of time that includes a random component (in addition to an exponential backoff)
