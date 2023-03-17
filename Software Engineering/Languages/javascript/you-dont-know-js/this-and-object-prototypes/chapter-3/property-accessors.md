# [Property Accessors](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch3.md#get)

## GET

* First look for property in object
* If property is defined in object, return value for object
* If property is not defined in object, traverse `Prototype` chain for object and see if property is defined for any `Prototype`s
* If property is still not found, return `undefined`
* However, impossible to distinguish if property is not found, or if `undefined` is the value assigned to a given property

## PUT

* If property exists in object
  * Is property an accessor descriptor - if so, call setter, if exists
  * Is property data descriptor where `writable: false` - if so, fail silently in non-strict mode or throw `TypeError` in strict mode
  * Otherwise, set the value of the property
* If property doesn't exist in object, involve `Prototype` chain (Chapter 5)
