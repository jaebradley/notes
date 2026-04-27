# [Vector Clocks](https://sookocheff.com/post/time/vector-clocks/)

## Total ordering with Lamport Clocks

* Each process maintains an integer value (starts with 0)
* Increment this value after every atomic event
* Send current local logical clock value on every outgoing message
* When a peer receives such a message, sets its own local logical clock as the max of the received logical clock value, or its local logical clock value (plus one)
  * In other words, its own logical clock is guaranteed to be greater than the received clock value
* Maintains consistency since the logical clock time when receiving the message is always _after_ the logical clock time associated with the message
* In the case where the sending clock is running ahead of the receiving clock, the receiving clock is advanced
  * Sending clock is at `78` and receiving clock is at `61`
  * On message reception, the message has clock value of `78`
  * Since `78` is greater than the reception clock valule, the receiving clock is increased to `79`
* In the case where the sending clock is running behind the receiving clock, no modification is made to the receiving clock
  * Sending clock is at `56` and receiving clock is at `61`
  * On message reception, the message has clock value of `56`
  * Receiving clock value increments by `1` to `62`

## Multiple possible orderings

* Lamport Clocks always report a consistent ordering of events
* They represent only one possible ordering
* Vector clocks implement the relation "happened before"
  * `a -> b` means `a` happened before `b` in all possible interleaving of events
* If `a` did not happen before `b` and `b` did not happen before `a` then the two events may have occurred concurrently (`a <-> b`)

## The Vector Clock algorithm

* Vector clocks represent clock values for a set of processes as an array
  * [Clock value for process 1, Clock value for process 2,...,Clock value for process N]
* Each process tracks their own logical time, as well as the logical time of all other processes in the network
* A vector clock of `[4, 7, 6, 12]` is attached to an event that occurs in process 2
  * Process `2` records a local clock value of `7` when the event was executed
  * Process `2`'s last known clock value for process `4` was `12`
  * Process `4`'s  local clock may have advanced past `12`, but from process `2`'s perspective, `12` is the greatest clock value it has seen for process `4`
* All clocks start at zero
* Local clock for a given process is implemented at least once before each local atomic event
* Each time a process sends a message, it includes a copy of its own timestamp vector
* Each time a process receives a message, it increments its own logical clock in its local vector clock by one 
  * Process also updates each element in its vector by taking the maximum of the value in its own vector clock and the value in the vector clock included in the received message
