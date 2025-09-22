# [Part 6: Time and Causality](https://lars.hupel.info/topics/crdt/06-time/)
* In a distributed system, events occurring across processes can only be ordered through messages
* If Process A performs some action and Process B performs some action, they know nothing about their respective orders
  * Only if Process A sends a message M to Process B do we know that sending the message must have happened before receiving that mesasge

> If a and b are events in the same process, and a comes before b, then a → b.

> If a is the sending of a message by one process and b is the receipt of the same message by another process, then a → b.

> If a → b and b → c then a → c.

> Two distinct events a and b are said to be concurrent if neither a → b nor b → a.

* If two events are concurrent, then they cannot causally affect each other

 
