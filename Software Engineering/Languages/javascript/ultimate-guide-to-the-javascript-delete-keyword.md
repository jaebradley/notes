# [The Ultimate Guide to the JavaScript Delete Keyword](https://medium.com/better-programming/javascript-delete-keyword-in-detail-4bdcf32dcdd80

* The common misconception about the delete keyword is that it's used to free the memory occupied by the property in the browser
* When the delete keyword is applied on a specific (configurable) object property
  * The property is dereferenced from the object
  * When garbage collection is executed, these dereferenced properties are made available
* Delete returns `true` if property is deleted or not found
  * It returns `false` if property cannot be deleted
* `defineProperty` method defines properties on a given object
  * Can set the `value` of the property, whether it's `writable`, whether it's `enumerable` and whether it's `configurable`
* Error is returned if attempting to redefine unconfigurable property
* If a property is added to global scope without a `var` keyword it can be deleted as these properties are marked as configurable
  * Properties added to global scope with a `var` keyword are marked as unconfigurable
* Deleting elements from an array doesn't reduce the length of the array - it just marks the element as `undefined`
