# [Count vs. Length vs. Size](https://mensfeld.pl/2014/09/activerecord-count-vs-length-vs-size-and-what-will-happen-if-you-use-it-the-way-you-shouldnt/)

* `count`
  * Executes `COUNT(*)` SQL query
  * Value is not memoized - it's recalculated each time
  * Quicker than `length` since `length` requires that collection is loaded in memory
* `length`
  * With lazy-loaded collection, `length` first requires collection to be loaded into memory, *then* the length of the collection is used
  * With already-loaded collection, performance isn't bad
* `size`
  * If collection is not loaded, it will use `count` and perform query
  * If collection is loaded, it will use `length` and not perform query
