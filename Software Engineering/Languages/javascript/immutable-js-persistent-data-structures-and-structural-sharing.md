# [Immutable.js, Persistent Data Structures And Structural Sharing](https://medium.com/@dtinth/immutable-js-persistent-data-structures-and-structural-sharing-6d163fbd73d2)

* Imagine an object that has `100k` items - updating a single value and copying that state can take `~130ms`
* `Object.assign` results in JavaScript shallowly copying each property from the source to the destination object one property at a time (in the previous case, it's `100k` properties)
* This shallow copying is so that the two objects are not related in any way (not true if the property values are objects, which are then shared by reference), since objects are mutable in JavaScript
* The equivalent `100k` item example takes `Immutable.Map` `1.2ms` to execute

## Persistent Data Structures

* The idea is to return a newer version of the current data stucture, instead of updating the original structure in-place
* Imagine storing a `Map` of data as a trie

```javascript
cosnt data = {
  to: 7,
  tea: 3,
  ted: 4,
  ten: 12,
  ...
}
```

* `t` -> `o` -> `7`
* `t` -> `e` -> `a` -> `3`
* `t` -> `e` -> `d` -> 4
* If updating `tea` from `3` to `14`, can construct a new trie from previous trie - only need to reconstruct `4` nodes
  * New root node that points to new `t` node (root node will point to previous child nodes)
  * `t` node, which points to new `e` node
  * `e` node, which points to new `a` node
  * `a` node points to a new value - `14`
* All other nodes can be reused - this is called "structural sharing"
* `Immutable.Map` creates tree where each node can have up to `32` branches so when a single item is updated, only a few nodes need to be recreated
* This re-use is only possible because persistent data structures are immutable - if they were mutable, some type of shallow copying would need to take place (i.e. same strategy as `Object.assign`) as a previous `Map` could update a node value that would impact the, theoretically, reused node

