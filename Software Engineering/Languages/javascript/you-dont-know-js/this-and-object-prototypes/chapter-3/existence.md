# [Existence](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch3.md#existence)

* `propertyName in object` will check if property is in the object or if it exists at a higher level of the `Prototype` chain
* `hasOwnProperty` checks to see if the property exists solely in the object or not and does not check the `Prototype` chain
  * Accessible for all objects that have access to `Object.prototype` chain - but can create objects that don't have access to `Object.prototype` chain via `Object.create(null)`
  * So safest way to use `hasOwnProperty` is to call `Object.prototype.hasOwnProperty.call(object, property)`
