# [Immutability](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch3.md#immutability)

* `writable: false` + `configurable: false` creates a constant (cannot be modified or deleted)
* `Object.preventExtensions` - prevents an object from having new properties added to it, but does not modify existing properties
* `Object.seal` - `Object.preventExtensions` + `configurable: false` on all existing properties
  * This ensures that object cannot have new properties and that existing properties cannot be deleted
  * Values can still be modified
* `Object.freeze` - `Object.seal` + `writable: false` on all non getter / setter properties
