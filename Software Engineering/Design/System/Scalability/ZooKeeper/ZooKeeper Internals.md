# [ZooKeeper Internals](https://zookeeper.apache.org/doc/r3.5.1-alpha/zookeeperInternals.html)

## Guarantees, Properties, and Definitions

* Reliable delivery: if a message is delivered by one server, it will be eventually delivered by all servers
* Total order: If a message a is delivered before message b by one server, a will be delivered before b by all servers
* Causal order: if message b is sent after message a has been delivered by the sender of b, message a must be ordered before b
* Relies on TCP, specifically the ordered delivery property of TCP
  * Data is delivered in the same order it is sent and a message is delivered only after all messages sent before it have been delivered
  * Corollary is that if the message is lost, all messages after that message are also lost
* Packets are a sequence of bytes sent through a FIFO channel (like a TCP connection)
* Proposals are units of agreement
  * Proposals are agreed upon by exchanging packets with a quorum of ZooKeeper servers
  * Most proposals contain messages, however the NEW_LEADER proposal is an example of a proposal that does not correspond to a message
* Message are a sequence of bytes that are atomically broadcast to all ZooKeeper servers
* ZooKeeper guarantees the total order of messages, and it also guarantees a total order of proposals
* ZooKeeper exposes the total ordering using a ZooKeeper transaction id, which all proposals are stamped with when the proposal is proposed
* Proposals are sent to all ZooKeeper servers and committed when a quorum of them acknowledge the proposal
* If a proposal contains a message, the message will be delivered when the proposal is committed
* Acknowledgement means the server has recorded the proposal to persistent storage
* Quorums have half the number of servers that make up the ZooKeeper service, plus 1
* The ZooKeeper transaction id has an epoch and a counter
  * The high order 32-bits are the epoch number and the low order 32 bits are for the counter
  * Epoch number represents a change in leadership, so each time a new leader is elected, it will have its own epoch number
  * To assign a unique ZooKeeper transaction id for each proposal, the leader increments the existing transaction id to obtain a unique transaction id for each proposal

## Leader Activation

* The following must hold when electing a leader
  * The leader has seen the highest transaction id of all the followers
  * A quorum of servers have committed to following the leader
* After leader election, a single server will be designated as a leader and start waiting for followers to connect
* Rest of the servers will try and connect to the leader
* The leader will sync up with followers by sending any proposals they are missing, or if a follower is missing too many proposals, it will send a full snapshot of the state to the follower
* Corner case where a follower has proposals ("U") that are not seen by a leader
  * Proposals are seen in order, so the proposals of U will have a transaction id that is higher than the transaction ids seen by the leader
  * "U" must have arrived after the leader election, or else it would have been elected the leader since it has seen a higher transaction id
  * Since committed proposals must be seen by a quorum of servers, and a quorum of servers that elected the leader did not see "U", the proposals have not been committed, so they can be discarded
* When the follower connects to the leader, the leader will tell the follower to discard "U"
* A new leader establishes a transaction id to start using for new proposals by getting the epoch (highest 32 bit value) of the greatest transaction id it has seen and setting the next transaction id where the highest 32 bits is the epoch + 1, and the lowest 32 bits is 0 (i.e. the counter)
  * After the leader syncs with a follower, it will propose a NEW_LEADER proposal
  * Once the NEW_LEADER proposal has been committed, the leader will activate and start receiving and issuing proposals
* A follower will ACK the NEW_LEADER proposal after it has synced with the leader
* A follower will only ACK a  NEW_LEADER proposal with a given transaction id from a single server
* A new leader will COMMIT the NEW_LEADER proposal when a quorum of followers have ACKed it
* A follower will commit any state it received from the leader when the NEW_LEADER proposal is COMMITTED
* A new leader will not accept new proposals until the NEW_LEADER proposal has been COMMITTED
* If leader election terminates erroneously, this is not a problem since the NEW_LEADER proposal was not committed since the leader did not have quorum

## Active Messaging

* Once a leader is elected, no other leader can emerge since no other leader will be able to get a quorum of followers
* The leader sends proposals, in the same order, to all followers
  * This order follows the order in which requests have been received
  * Followers must receive proposals in the same order since the leader is using FIFO channels
* Followers process messages in the order they are received
  * Messages will be ACKed in order and the leader will receive ACKs from followers in order
  * Implies that if message X has been written to non-volatile storage, all messages thtat were proposed before X have been written to non-volatile storage
* Leader will issue a COMMIT to all followers as soon as a quorum of followers have ACKed a mesasge
  * Since messages are ACKed in order, COMMITs will be sent by the leader (and received by the followers) in order
