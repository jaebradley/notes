# [Natives](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch3.md#chapter-3-natives)

## `Natives`

* Commonly used natives
  * `String()`
  * `Number()`
  * `Boolean()`
  * `Array()`
  * `Object()`
  * `Function()`
  * `RegExp()`
  * `Date()`
  * `Error()`
  * `Symbol()`
* Using the natives to construct values results in an `object` and *not* the underlying primitive
* `typeof new String("abc")` is `object` and not `String` or `string`

## `Class` property

* Values that are `typeof "object"` have an internal `Class` property
* This property is for "classification" and is not related to classes from class-oriented programming - it usually corresponds to the built-in native constructor
* This property cannot be accessed directly, but usually is revealed using `Object.prototype.toString`
* For primitives like `string`, `number`, and `boolean`, these values get "boxed", which is why their respective native classes are exposed as the `Class` property

```javascript
// examples of `toString`

Object.prototype.toString.call( null );			// "[object Null]"
Object.prototype.toString.call( undefined );	// "[object Undefined]"

Object.prototype.toString.call( "abc" );	// "[object String]"
Object.prototype.toString.call( 42 );		// "[object Number]"
Object.prototype.toString.call( true );		// "[object Boolean]"
```

## Boxing Wrappers

* Boxing is important because the primitive values by themselves don't have properties or methods (like `.length` or `toString`)
* By boxing / wrapping the primitive value in it's corresponding Native object, these helpers are no available for access
* There's basically no reason to use the object form directly - let boxing implicitly happen where necessary
* `new Boolean(false)` will always be `truthy` because it's an `object`
* Use `valueOf` to get the underlying primitive value from an object wrapper

## Natives as Constructors

* `Array(first, second, third)` is the same as `new Array(first, second, third)`
* However, if **one** number value is passed to `Array` it uses that as the `length` of the array
* Chrome serializes `new Array(3)` as `[ undefined x 3 ]` which implies that these slots exist, even when they don't
* Some array methods, like `join` use the `length` properly and *assuming* the slots exist
* Others, like `map` actually try to iterate through the slots
* To create array of actual `undefined` values and not just "empty slots", `Array.apply(null, { length: 3})`
  * This will essentially call `Array(undefined, undefined, undefined)`
* **Never, ever, under any circumstances** intentionally created and use these empty-slot arrays
