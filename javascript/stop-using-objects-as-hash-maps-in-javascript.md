# [Stop Using Objects As Hash Maps In JavaScript](https://medium.com/better-programming/stop-using-objects-as-hash-maps-in-javascript-9a272e85f6a8)

* Object keys can only be symbols or strings while Map keys can be objects, functions, or primitives
* Map has a `size` property so the lookup time is `O(1)` while it takes `O(n)` to get the length / size of an object (using `Object.keys`)
* Maps are optimized for frequent additions / removals of entries
* Maps can be directly iterated like `for (let [key, value] of myMap)` vs. objects which have to be iterated like `for (let key of Object.keys(myObject))` and then the `key` is used to lookup a value from the object
* Maps guarantee that keys appear in order of insertion
* A plain object may contain keys / properties because of its prototype which may cause collisions while a map does not contain any keys on creation
  * Can use `Object.create(null)` to create an object without any properties