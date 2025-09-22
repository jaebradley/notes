# [Part 6: Time and Causality](https://lars.hupel.info/topics/crdt/06-time/)
* In a distributed system, events occurring across processes can only be ordered through messages
* If Process A performs some action and Process B performs some action, they know nothing about their respective orders
  * Only if Process A sends a message M to Process B do we know that sending the message must have happened before receiving that mesasge

> If a and b are events in the same process, and a comes before b, then a → b.

> If a is the sending of a message by one process and b is the receipt of the same message by another process, then a → b.

> If a → b and b → c then a → c.

> Two distinct events a and b are said to be concurrent if neither a → b nor b → a.

* If two events are concurrent, then they cannot causally affect each other

 <img width="1508" height="840" alt="Screenshot 2025-09-22 at 07 51 22@2x" src="https://github.com/user-attachments/assets/8fd454b7-d438-496a-9c0f-811b4f30d40c" />

* 3 nodes (A, B, C)
* Events have numbers assigned to them and they have labels
 * If Node C sends a mesage to Node B then the sending event has label C1 and the receiving event has label B1
* C1 has caused the following events
 * B1
 * Everything that C does after C1 (even if B1 hasn't happened yet)
 * Everything that B does after B1
* Lamport clock
 * When sending a message from X to Y, increment the local clock of X and include the incremented value in the payload of the message
 * When receiving a message from X on Y, compare the local clock on Y with the one you've received from X, take whichever is the largest, and increment
* When C sends its first mesasge, it has a Lamport time of 0
 * Increments this time to 1 and sends that value in the message
 * B receives message from C, and has a local time of 0
 * B receives a time of 1, increments it, and thus B has a local time of 2

 >Any two object replicas of a CvRDT eventually converge, assuming the system transmits payload infinitely often between pairs of replicas over eventually-reliable point-to-point channels.

* Distributed network of nodes where each node stores the same CRDT
* At some point, Alice knows that the set contains `{1, 2}` and Bob knows that the set contains `{2, 3}`
* At some point, they send their state to each other
* Convergence means that if Alice and Bob keep sending each other updates, and these updates will be delivered at some point, they will end up in the same state

<img width="1540" height="562" alt="Screenshot 2025-09-22 at 08 03 56@2x" src="https://github.com/user-attachments/assets/14647c27-897a-4949-a4ea-d607302800a6" />
* Alice adds 2 to her set and Bob adds 3
* Alice's causal history is `{add(2)}` and Bob's causal history is `{add(3)}`
* When Alice sends her set / history to Bob, his history is now `{add(2), add(3)}`
* When these values are joined they will converge as a join operation shouldn't invent / destroy values from other nodes and should include the correct values at some point
