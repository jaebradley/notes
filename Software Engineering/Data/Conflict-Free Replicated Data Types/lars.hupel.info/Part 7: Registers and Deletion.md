# [Part 7: Registers and Deletion](https://lars.hupel.info/topics/crdt/07-deletion/)
* Example scenario
  * Alice and Bob both start with the set {2}.
  * Alice adds 1 to the set.
  * Alice deletes 1 from the set.
  * Bob adds 1 to the set.

* Can express values as a tuple of values a la `(value, time)`
  * Alice and Bob start at `{2 -> (false, 1)}`

<img width="1672" height="450" alt="Screenshot 2025-09-23 at 21 58 39@2x" src="https://github.com/user-attachments/assets/10899ca0-b8cb-4fa3-a4c5-0987bc9da960" />

* Bob's timestamp for the `1` value is higher so his addition wins
* Vector clocks increment when an event occurs
* Each node keeps its own clock
* Each node also keeps each other node's clock (hence, a vector of clocks)
* When a message is sent, the sending node only increases its own clock, but includes a copy of the entire vector of clocks in the message
* When a message is received, the receiving node
  * Increments its own clock,
  * And for everyone else's clock, takes the maximum of the receiving node's vector values and the received vector values

## Vector Clocks
<img width="1556" height="808" alt="Screenshot 2025-09-23 at 22 01 36@2x" src="https://github.com/user-attachments/assets/df7e0d9f-81d5-42cf-97da-4fb7c09efa08" />

* First message from C to B causes C to increase its own clock to 1
* When B receives message from C it updates it's knowledge of C's click to 1 and increases its own clock to 1
* B sends a message to A
  * B increases itw own clock to 2 but keeps all other vector entries unchanged
  * It also sends its knowledge of C's clock to A
* When A receives its second message, C's vectors are B = 3 and C = 3
  * A's knowledge is B = 2 and C = 1
  * A updates its vector to A = 3, B = 3, and C = 3
* These vector clocks are identical to grow-only counters

## Write Conflicts
1. Alice’s vector clock is ≥ Bob’s vector clock
  * This comparison works exactly the same way as the partial ordering we’ve defined for maps, that is, each entry in Alice’s vector clock must be ≥ the corresponding entry in Bob’s vector clock.
  * If this is true, then we know that Alice’s causal history contains all the events of Bob’s causal history.
  * In other words, Alice has complete knowledge of everything that Bob has seen so far, which means that Alice’s state of the register wins.
1. Conversely, if Bob’s vector clock is ≥ Alice’s vector clock, then Bob’s state wins.
1. If the two vector clocks are incomparable, then we have a concurrent write.
  * This happens when there is network connection loss right after the start of the session
  * Keep both writes
    * So for the value 2, Alice's clock is at 1 and Bob's clock is at 0
    * For the value 3, Alice's clock is at 0 and Bob's clock is at 1
  * Future writes can merge all values
  * If the future write has a larger clock knowledge of all the nodes, then the previous historical values can be discarded
    * All of the nodes synced their values successfully and the author node was the last person to perform a write
