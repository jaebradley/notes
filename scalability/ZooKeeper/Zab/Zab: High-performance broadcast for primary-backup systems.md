# [Zab: High-performance broadcast for primary-backup systems](https://ieeexplore.ieee.org/document/5958223)

## Introduction

* Each state change is incremenetal with respect to the previous state
* State changes cannot be applied in any arbitrary order
* State changes are idempotent and applying the same state change multiple times does not lead to inconsistencies as long as the application order is consistent with the delivery order
  * Guaranteeing at-least once semantics is sufficient and simplifies the implementation
* Transaction identifiers are pairs of values - instance value and the position of a given transaction in the sequence broadcast by the primary process for that instance
  * Only the process having accepted the transaction with the highest identifier may have to copy transactions to the new primary
  * A new primary is able to decide which transactions to recover and from which process simply by collecting the highest transaction identifier from each process in a quorum

## System Model

* Processes use bidirectional channels to exchange messages
* Processes have a pair of buffers (input / output)
* A process A only receives a message from another process B only if process B has sent the message
* If there is a message M1 and a preceding message M0, process A will received M0 before M1
* The input buffer of a process for a channel contains messages from, at most, one iteration
  * An "iteration" is the sending of messages during the lifetime of a channel
* Use TCP connections to establish connection between processes
  * The nature of TCP guarantees order and also guarantees that a process only has messages froma  single iteration

## Problem Statement

* Only a primary is able to broadcast
* There is at most one primary process
* Primaries have some sequence over time
* Primary only starts generating state updates once the Zab layer has indicated that recovery has completed
* There is an `instance` variable that a primary uses to determine its instance
* The primary uses this `instance` value to set the epoch of transaction identifiers when broadcasting
  * The `instance` values are unique for different primary values
* Transactions are the state changes a primary propagates to the backup processes
* Each transactions identifier has two components - an epoch and a counter
  * The epoch value of a transaction is equal to the `instance` value for a primary
  * Each new transactions increments the counter
  * This guarantees that if a new primary is elected, all the transactions from the previous primary will be less than any new transactions broadcast by the new primary
* When a primary broadcasts a transaction, it is not guaranteed to succeed if the primary crashes or there is a change in primary
  * From the sequence of state changes a primary broadcasts, only some prefix of the sequence of state updates is delivered
  * When a process delivers a transaction, a process adds it to a `txns` set

### Core Properties

* If some process delivers a transaction, then some process has broadcast the transaction
  * Guarantees that no transaction is created spontaneously / corrupted
* If a process delivers transaction 0 before transaction 1, then any process that delivers transaction 1 must also deliver transaction 0 and deliver transaction 0 before transaction 1
  * Guarantees that transactions are delivered in a consistent order
* If some process A delivers transaction 0 and some process B delivers transaction 1, either process A delivers transaction 1 or process B delivers transaction 0
  * This agreement property disallows "runs" in which two processes deliver disjoint sequences of transactions
* A primary has to guarantee state updates generated are consistent
* A primary consequently can only start broadcasting in an epoch once it has delivered the transactions of previous epochs

### Algorithm Description

* Two roles, leader and follower
* A leader concurrently executes the primary role and proposes transactions according to the order of broadcast calls of the primary
* Followers accept transactions according to the steps of the protocol
  * A leader also executes the steps of a follower
* Each process implements a leader oracle that provides the identifier of the prospective leader

#### Discovery

* Follower sends prospective leader last epoch / primary instance it has seen
* Leader proposes a new epoch that is greater than any epoch it received from the quorum of followers
* Once followers receive the new epoch from the prospective leader, if this proposed new epoch is less than the last new epoch proposer the follower acknowledged, then acknowledge this new epoch from the prospective leader
  * Follower acknowledges the new epoch proposal by sending an ack to the prospective leader containing the followers history of accepted transactions
* Once the propsective leader has received confirmation from each follower in the quorum, it selects the history of one follower in the quorum such that the follower has the greatest accepted transaction identifier

#### Synchronization

* Leader proposes themselves as new leader with the new epoch and proposed history to all followers in the quorum
* Follower starts a new iteration if the last new epoch proposal follower acknowledged is not equal to the new epoch
* If the last new epoch proposal follower acknowledged is the same as the new epoch
  * The follower sets the last new leader proposal follower acknowledged to the new epoch
  * For each transaction in the history, it adopts the history from the leader
  * All of these steps are done atomically
* Follower acks the new leader proposal
* Once leader receives acks from a quorum of followers, it sends commit message to followers
* Follower receive commit message and delivers / commits all transactions in the initial history of the leader

#### Broadcast

* Leader proposes to all folowers in the quorum in increasing order of transaction id
* Followers accept proposals from the leader and appends them to the follower's history
* Upon receiving acks from quorom for a given proposal, the leader sends a commit to all followers
* Follower commits transaction once it receives the commit and has committed all previous transactions
* Upon receiving a `CEPOCH(e)` message (the last "promise") from a follower, the leader proposes back a new epoch and a new leader

## Zab in Detail

* The primary picks values to broadcast in FIFO order and creates a transaction
* Upon receiving a request to broadcast a transaction, a leader sends a proposal
* The followers accept the proposal via an acknowledgement message
* Followers do not send acknowledgement until they write the proposal to local stable storage
* When a quorum of processes have accepted the proposal, the leader issues a commit message
* When a process receives a commit message for a proposal, the process delivers / commits all undelivered proposals with a transaction id less than the transaction id in the commit message

### Establishing a new leader

* Once a process determines that it is a prospective leader it starts a new iteration in the Discovery phase
* It initially collects the latest epoch of a quorum of followers, proposes a later epoch, and collects the latest epoch and highest transaction id of each of the followers in the quorum
* Leader completes Discovery phase once it selects the highest from a follower with latest epoch and highest transaction id
* In Synchronization, the leader proposes itself as the leader of the new epoch and sends a `NEWLEADER` proposal, which contains the initial history of the new epoch
  * Leader will send each follower any transactions that it is missing
* Leader is establishes once it receives acks to the `NEWLEADER` proposal from quorum of followers. Leader then commits the new proposal

### Leading

* Leader and followers exchange periodic heartbeats
* If the leader does not receive heartbeats from a quorum of followers within some timeout interval, the leader renounces leadership of the epoch and transitions to the election state

### Following

* Once a follower accepts a new epoch proposal for some epoch X, it does not send acknowledgement for any other new epoch proposal for the same epoch X
  * This guarantees that no two processes can become esetablished leaders for the same epoch X
