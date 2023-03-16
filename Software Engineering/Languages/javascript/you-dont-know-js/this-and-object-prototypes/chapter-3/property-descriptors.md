# [Property Descriptors](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch3.md#property-descriptors)

* `Object.getOwnPropertyDescriptor(obj, propertyName)`
  * function returns object with these properties that make up the "definition" of that property
  * `value`
  * `writable` - indicates ability to change value of property
    * In strict mode, this will throw a `TypeError`
  * `enumerable` - ensures that property will show up in `for..in` loop, for example
  * `configurable` - indicates if descriptor definition is modifiable via `defineProperty`
    * Changing `configurable` to `false` is a one-way decision and cannot be undone
    * If `configurable` is `false`, `writable` can always be changed from `true` to `false` (but not in the reverse direction)
    * When a property has `configurable` equal to `false`, it cannot be `delete`d
      * `delete` removes the reference of some object property to some object / function / etc. And thus, it *could* be garbage-collected - but doesn't guarantee that memory is freed up in any way
* `Object.defineProperty` to add new property or modify existing one (if it's `configurable`)
