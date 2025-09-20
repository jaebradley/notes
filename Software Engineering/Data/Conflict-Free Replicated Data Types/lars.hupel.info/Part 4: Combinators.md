# [Part 4: Combinators](https://lars.hupel.info/topics/crdt/04-combinators/)
* When merging maps from the distributed counter example, end up computing the maximum of values for the keys and then using the value
  * This is independent of the type of the key or value
* So to compute the `join` of two maps, we need to compute the `join` of the values
* State-based CRDTs are data types that have a join-semilattice and only support monotonic operations
  * Monotonic updates means that the system will eventually converge
* `Set<T>` is equivalent to ` Map<T, void>`
* For one map to be smaller than another map, the other map needs to have at least the same keys defined
  * Then for each value, need to check that the other map's value is larger or equal
