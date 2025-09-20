# [Latices](https://lars.hupel.info/topics/crdt/03-lattices/)
* Lattice is a structure that has an operation ("join")
* Join operation merges two values and produces a bigger value
* For sets, the "join" operation is a set union
  * Always mergable
  * Order doesn't matter
  * Result is always a superset of both sets
* Lattices also have another operation: `meet`, which is the opposite of join
  * For sets, this would be `intersection`
* When we have two values and join them, we should obtain something that is greater-than-or-equal-to the original values
  * Thus, we can define what less-than-or-equal-to means based on the join operator
  * In other words, joining two values means producing the smallest possible value that's just larger-than-or-equal-to the inputs
* If set A = `{1, 2}`  and set B = `{1, 2, 3}` then the union of set A and set B is B since set A is included in set B
* Distributed counter example
  * Use a mapping like `{ user1: 0, user2: 0 }`
  * Internet connection fails
  * User 1 increments the counter
    * User 1's version of the counter looks like `{ user1: 1, user2: 0 }`
  * User 2 increments the counter
    * User 2's version of the counter looks like `{ user1: 0, user2: 2 }`
  * Internet connection is restored
  * Merge the two maps by taking the max of each key/value pair
  * Total value of the counter is the sum of all the values in the map
  * Only works if users agree that they will never increment another user's counter directly
  * Can simply this further by having each user increment their own counter and then merge everybody's counter together
    * `{ user1: 0 }` and `{ user2: 0 }`
    * Internet connection fails
    * `{user1: 1 }` and `{user2: 1 }`
    * Merge these mapping by checking for keys that are only present in the other mappings and copy the keys and values (unchanged) into a single mapping
  * To counteract retransmission (`user1 does not receive acknowledgement from user2`) retransmit the snapshot of the known state `{ user1: 1 }`  instead of an increment operation (this operation is idempotent)
