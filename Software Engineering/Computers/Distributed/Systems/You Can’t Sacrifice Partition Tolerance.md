# [You Can’t Sacrifice Partition Tolerance](https://codahale.com/you-cant-sacrifice-partition-tolerance/)

## Consistency

* Atomic consistency implies the existence of a total order on all operations such that each operation looks as if it were completed at a single instant
* Must looks like requests were executing on a single node, responding to operations one at a time
* Consistency is when an Update is applied to all relevant nodes at the same logical time
* Consistency that is both instantaneous and global is impossible

## Availability

* A continuously available system results in a response (non-500) for every request received by a non-failing node
* If a single piece of data lives on five nodes and all five nodes die, that data is gone
  * Any request which required it can no longer be handled

## Parition Tolerance

* To model partition tolerance, network will be allowed to lose arbitrarily many messages sent between nodes
* Any pattern of message loss can be thought of as a temporary partition separating the communicating nodes at the exact instant the message is lost
* For a distributed system to not require partition-tolerance, it would have to run on a network which is guaranteed to never drop messages (or deliver them late)
  * These systems do not exist

## Choosing Consistency Over Availability

* It will preverse the guarantees of its atomic reads and writes by refusing to respond to some requests
* It may refuse writes, or only respond to reads and writes for pieces of data whose leader node is inside the partition component
* Example of strongly consistent system - atomic counter

## Choosing Availability Over Consistency

* Will respond to all requests, potentially returning stale reads, accepting conflicting writes
* Inconsistencies resolved via causal ordering mechanisms like vector clocks

## Thought Experiment

* Distributed system with three nodes
* Keeps track of a single piece of data
* System is partitioned into two components
  * Component 1: A & B
  * Component 2: C
* Write request arrives at C to update the single piece of data
* C can accept the write, knowing that neither A nor B will know about this new data until the partition heals (availability)
* C can refuse the write, knowing that the client might not be able to contact A or B until the partition heals (consistency)

## Yield

* The probability of completing a request
* Like uptime, but slightly different, as it relates to user experience
* Not all seconds have equal value
* Being down for one second when there are no queries has no impact on users or yield, but does decrease uptime
* Being down for one second at peak times has the same impact to uptime, but has more of an impact on users and yield

## Harvest

* Example is a search engine where there are three nodes
* Normally, a search result for a particular term would combine results from all three nodes
* Imagine that one of the nodes is unavailable
* Now only two of the nodes would return results for the same term
* So the "harvest" is 2/3 - data available divided by the totla data

## Favoring Yield or Harvest

* Replicated systems tend to map "faults" to reduced capacity and thus reduced "yield"
* Partitioned systems tend to map faults to reduced "harvest"
  * Part of ths database may temporarily disappear, but capacity in processable queries per second remains the same
