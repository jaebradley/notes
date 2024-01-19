# [Queues Don't Fix Overload](https://ferd.ca/queues-don-t-fix-overload.html)

* Most projects can be mentally modeled like a bathroom sink
* Water from faucet is analogous to the "edge" of a system that interfaces with external users
* Overload for the system occurs when a lot of water comes out of faucet and sink starts to overlow
  * News site when big events occur
* Deal with temporary overload with things like caches (to reduce the work required)
  * Or with a huge buffer / queue (in this mental model, the sink is the buffer / queue)
* Problem occurs when prolonged overload is encountered
* Buffers / queues can't deal with the increased pressure
* In the sink analogy, water starts overflowing
* Investigation of queue, database, etc takes a lot of time
* Each optimization only delays things crashing by 2-3 days more
* Problems still happen, but now they are weeks apart between failures, which makes optimization more difficult
* Vertical scaling (i.e. buying a bigger sink) still leads to failures because the downstream bottleneck still exists
  * In the sink mental model, downstream bottleneck is a narrow sink pipe that all water ("traffic") traverses
* Ultimately, have to pick blocking on input (back-pressure) or dropping data (load-shedding)
  * Bouncers in front of clubs (blocking on input)
  * Water spillways to go around dams (load-shedding)
  * System-wide flow control to keep operations safe
* "We never shed load because that makes stakeholders angry, and we never think about back-pressure. Usually the backpressure in the system is implicit: 'tis slow."
* When operations take too long and block stuff up, people introduce a queue into the system
  * Application is now fast (even though you need to redesign the whole interface and interactions and reporting mechanisms are now asynchronous)
* Queue spills over and results in data loss
  * "Add more workers"
  * "Add persistence, so that when it crashes, no requests are lost"
* The real problem is that everyone used queues as an optimization mechanism
  * New problems occur when queues are introduced like assuming tasks can be replayed and are durable
  * New places that can time out / error cases, so now there are new ways that are required to detect failure and communicate those failures back to the user
* Ultimately, have to pick one of two things - stop people from inputting stuff into the system, or shed load
* When people blindly apply a queue as a buffer, all they're doing is creating a bigger buffer to accumulate data that is in-flight, only to lose it sooner or later
  * You're making failures more rare, but you're making their magnitude worse
* Queues are often applied in cases of bad system engineering, forcing an 18-wheeler to go through a drinking straw and
  * In the end, queues just make things worse
  * Using a queue is like building a dam to solve flooding problems upstream of the dam
