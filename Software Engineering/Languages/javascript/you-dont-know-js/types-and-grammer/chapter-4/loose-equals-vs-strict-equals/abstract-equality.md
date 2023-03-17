# [`Abstract Equality`](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch4.md#abstract-equality)

* Section `11.9.3` of the `ES5` spec
* `NaN` is never equal to itself
* `+0` and `-0` are equal to each other
* `==` between `object`s (including `function`s and `array`s) are only equal if the both have the same reference
  * No coercion occurs
  * This implies that `==` and `===` behave identically in the case where two `object`s are being compared
* If `==` is used to compare values of different types, one (or both) of the types will need to be **implicitly** coerced

## `string`s to `number`s

* If the first value in `==` is a `number` and the second value is a `string`, the `string` is coerced to a `number` using the `ToNumber` abstract operator
* If the first value in `==` is a `string` and the second value is a `number`, the `string` is coerced to a `number` using the `ToNumber` abstract operator
* When comparing a `string` to a `number`, the `string` is coerced

```javascript
var a = 42;
var b = "42";

a === b;	// false
a == b;		// true
```

## Anything to `boolean`

* `boolean`s get the `ToNumber` abstract operator applied to it

```javascript
var a = "42";
var b = true;

// 1. ToNumber operates on b - it returns 1
// 2. "42" is now compared against 1
//    a. Since this is a string vs. number comparison, ToNumber operates on "42" and returns 42
// 3. 42 is now compared against 1 (which is false)
a == b;	// false

// The weird thing is that if b was false, the == comparison would still be false!
// This is because b gets coerced to 0, etc. etc.

b = false

a == b // false
```

* **Don't use `== true` or `== false` ever**

## `null` to `undefined`

* If either operator is `null` or `undefined`, return `true`
* This means that `null` and `undefined` are indistinguishable using `==`

```javascript
// These are the same

if (a == null) {
  // blah blah
}

if (a === null || a === undefined) {
  // blah blah
}
```

## `object`s to `string`, `number`, or `boolean`

* `object`s are coerced using the `ToPrimitive` abstract operator

```javascript
var a = 42;
var b = [ 42 ];

// 1. b gets coerced to a string ("42") via it's toString property
//    a. The valueOf property just returns the array and ToPrimitive needs a string
// 2. Since b is a string and a is a number, b gets coerced via the ToNumber abstract operator to 42
// 3. 42 is equal to 42
a == b;	// true
```

* `null` and `undefined` cannot be wrapped in an `Object` (i.e. `Object(null)` just returns a new `object`)
  * This means that `null` is not `==` to `Object(null)` (same for `undefined`)

## Be careful of overriding `valueOf`

```javascript
Number.prototype.valueOf = function() {
  return 3;
};

// new Number(2) creates an object
// The object gets coerced by ToPrimitive
// The valueOf property is evaluated
// 3 is returned
// 3 is equivalent to 3
new Number(2) == 3; // true
```

```javascript
var i = 2;

Number.prototype.valueOf = function() }
  return i++;
}

var a = new Number(100);

// THIS IS ACTUALLY TRUE
if (a == 2 && a == 3) {
  // DAMNIT HOW DID WE GET HERE?
}
```

## Edge Cases

* `"0" == false` is `true`
  * `false` => `0`
  * `"0"` => `0`
* `false == ""` is `true`
  * `false` => `0`
  * `""` => `0`
* `false == []` is `true`
  * `false` => `0`
  * `[]` => `""` via `toString`
  * `""` => `0`
* `"" == 0` is `true`
  * `""` => `0`
* `"" == []` is `true`
  * `[]` => `""` via `toString`
* `0 == []`
  * `[]` => `""` via `toString`
  * `""` => `0`
* `[] == ![]` is `true`
  * `![]` coerces to `false`
  * This translates the equality to `[] == false` which is `true`
