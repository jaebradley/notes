# [Conflict-Free Replicated Data Types (CRDTs)](https://read.thecoder.cafe/p/crdt)

## G-Counter (Grow-Only Counter)
* A distributed "likes" counter for a post
* Each node keeps a mapping of the number of likes that each node has seen
  * 5 likes total, distributed over two nodes

```
Node 1: [2, 3]
Node 2: [2, 3]
```

* If Node 1 receives a new like for the post, it increments its own slot
  * Node 2 is temporarily out of sync

```
Node 1: [3, 3]
Node 2: [2, 3]
```

* To synchronize, both nodes **merge** their mappings by taking the maximum count of likes seen by each node

```
[max(3,2), max(3,3)] = [3, 3]
Node 1: [3, 3]
Node 2: [3, 3]
```
* This algorithm is deterministic and order-independent, so no matter when, or how often, the nodes sync, they always end up in the same state

## PN-Counter (Positive-Negative Counter)
* Similar to G-Counters but there are two arrays - one for incrementing likes and one for decrementing likes

```
-- Increase
Node 1: [2, 3]
Node 2: [2, 3]

-- Decrease
Node 1: [0, 0]
Node 2: [0, 0]
```
* So if Node 1 received a new like and Node 2 received a dislike, then before merging the per-node states would look like
```
-- Increase
Node 1: [3, 3]
Node 2: [2, 3]

-- Decrease
Node 1: [0, 0]
Node 2: [0, 1]
```
* The merge rule is the same - take the per-node maximum for both the incrementing like count and the decrementing like count

```
-- Increase
Node 1: [3, 3]
Node 2: [3, 3]

-- Decrease
Node 1: [0, 1]
Node 2: [0, 1]
```

## Use Cases
* Useful for systems that emphasize totally available systems that value availability over strong consistency
  * CDNs keep content near users and sync to the origin later

## Different Types of CRDTs
* State-based CRDTs merge when propagating the full state
* Operation-based CRDTs merge when propagating the update operations
* Grow-Only Counters and Positive-Negative Counters are examples of state-based CRDTs
  * Nodes exchanged their entire state
  * Merging must lead to the same state regardless of the order in which states are merged (commutative)
  * Merging state with itself doesn't change the output state (idempotency)
  * Merging state processed in different groupings at a time leads to the same state (associativity)
  * Each merging monotonically increases the internal state (counter)
  * Merges tend to use operations like a max for numbers or unions for sets
* Example of an operation-based CRDT is a Last-Writer-Wins Register, which stores a single value
  * Updates are resolved using a logical timestamp (like a Lamport clock) + a tie-breaker (like a node ID)
  * When a node writes a value, it broadcasts an operation (like `value, timestamp, node ID`)
  * When a node receives a value, it applies an update if the value is associated with a `timestamp, node ID` combination greater than the one it currently holds
