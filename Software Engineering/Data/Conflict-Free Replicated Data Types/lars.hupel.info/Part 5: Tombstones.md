# [Part 5: Tombstones](https://lars.hupel.info/topics/crdt/05-tombstones/)

## Example
* Alice and Bob both start with the set {2, 3}.
* Internet connection fails.
* Alice adds 1 to the set.
* Bob adds 1 to the set.
* Alice deletes 1 from the set.
* Internet connection is restored.
* Is the set `{1, 2, 3}` or `{2, 3}`

## The Ghost of Values Past
* Two-Phase Set: a value travels through the two phases of being a member of the set and having been a member of the set
* In the above example, the set is `{2, 3}` as Alice's deletion takes priority over Bob's addition
* Time does not play a role - if Alice deleted before Bob added, the result would still be `{2, 3}`
* Two-Phase Sets are implemented as a pair of Grow-Only Sets for the added and removed elements
  * The removed elements are always a subset of the added elements
  * The set of currently-present elements is the set difference of the added elements less the removed elements
  * Since the removed elements set contains a "memory" of old values that will never again appear in the Two-Phase Set it is called the tombstone set

## Two-Phase Sets are also Maps
* Added and removed elements are keys in a map where their corresponding boolean values indicate whether the value has been deleted
* So in the above example, Alice's mapping of values would look like `{1: true, 2: false, 3: false }` and Bob's mapping of values would look like `{1: false, 2: false, 3: false}`
* To merge values, `true` always has priority over `false` values
  * This also implies that values go from `false` to `true` and never the opposite
