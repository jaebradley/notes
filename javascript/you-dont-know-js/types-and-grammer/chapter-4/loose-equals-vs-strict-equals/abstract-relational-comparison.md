# [`Abstract Relational Comparison`](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch4.md#abstract-relational-comparison)

* `a < b`
  * `ToPrimitive` is called on both values
  * If either call is **not** a string then both values are coerced to `number`s using `ToNumber`

## `object` compared to `object`

```javascript
var a = { b: 42 };
var b = { b: 43 };

// a => [object Object]
// b => [object Object]
a < b;	// false

// a and b aren't equal because they don't have the same reference
a == b;	// false

// a => [object Object]
// b => [object Object]
a > b;	// false

// a <= b means evaluate b < a first and then negate the result
a <= b;	// true

// a >= b means evaluate a < b first and then negate the result
a >= b;	// true
```

* **JavaScript considers `<=` as "not greater than"**
