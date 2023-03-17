# [Index as a key is an anti-pattern](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318)

* A `key` is the only thing React uses to identify `DOM` elements
* If you push an item to the list, or remove something in the middle and the `key` is the same as before, React assumes that the `DOM` element represents the same component as before
* Production solution would handle distributed creation of items, like [`shortid`](https://github.com/dylang/shortid) and then use the generated unique id for every item and use it as the `key` when rendering the list
* Using the `index` as the `key` is justifiable when all are true
  * the list and items are static - they are not computed and do not change
  * the list items have no ids
  * the list is *never* reordered or filtered
