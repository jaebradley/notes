# [Enumeration](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch3.md#enumeration)

* If `enumerable` is set to valse, it won't show up in a `for..in` loop, but will be revealed via `in` operator existence check
* `for..in` loops applied to arrays can give unexpected results in that enumeration of arrays include not only numeric indices but also any enumerable properties
  * It's a good idea to only use `for..in` loops only on objects, and traditional `for` loops with numeric index iteration for the values stored in arrays
* `propertyIsEnumerable` tests whether given property name exists directly on the object and has `enumerable: true`
* `Object.keys` returns array of all enumerable properties directly on the object (no prototype chain)
* `Object.getOwnPropertyNames` returns an array of all properties, enumerable or not, directly on the object (no prototype chain)
