# [Values](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch2.md)

## Arrays

* Using `delete` on `array`, will remove that slot from the `array`, but it will not update `length`
* Arrays are also objects that can have `string` keys / properties added to them that don't count toward `length`
* However, if a `string` key can be coerced to a `base-10` number, it will set the value for the numerical index

## String

* `string`s are *not* an `array` of characters
* `string`s are immutable while `array`s are mutable
  * For example, can reverse `array` using in-place `reverse` method
  * Can't work for `string`s because they are immutable
    * Workaround is to split `string` into an `array`, `reverse` *that* array, and rejoin the array into a `string`
    * This doesn't work for `string`s with complex unicode characters

## Number

* `number` literals can also be expressed in other bases, like binary, octal, and hexadecimal
  * `ES6` `strict mode` `0363` form for octals is being replaced by `0o363;`

```javascript
0xf3; // hexadecimal for: 243
0Xf3; // ditto

0363; // octal for: 243
```

* `0.1 + 0.2 === 0.3;` is `false`
* `0.1` and `0.2` are represented in `binary` floating point (so they're not exact)
* Use `Number.EPSILON` to use as a tolerance value

```javascript
function numbersCloseEnoughToEqual(n1,n2) {
	return Math.abs( n1 - n2 ) < Number.EPSILON;
}

var a = 0.1 + 0.2;
var b = 0.3;

numbersCloseEnoughToEqual( a, b );					// true
numbersCloseEnoughToEqual( 0.0000001, 0.0000002 );	// false
```

* `Number.isInteger` to test if value is an integer
* `Number.isSafeInteger` to test if value is safe integer
  * `Number.isSafeInteger( Math.pow( 2, 53 ) );` will fail
* `NaN`
  * More accurate to think of this as "invalid number", "failed number" or "bad number"
  * `typeof` is `number`
  * Only value that is not equal to itself
  * Global `isNaN` function checks to see if the value is a number or not vs. the `NaN` value
    * Will return `true` for `NaN`
    * Will also return `true` for `"foo"`
  * `Number.isNaN` does what we'd want - identifies `NaN` value
* `Infinity` stays infinite
* `Infinity` / `Infinity` leads to a `NaN`
* Has `-0` and `0`
* `var a = 0 / -3;` will lead to a `-0`
* When `-0` is stringified, it is coerced to a `0`
* Going from `number` to `string` is fine for `-0`
* Negative zero is useful if sign of number indicates the direction of the numbers coming in
  * If a variable arrives at zero and it loses its sign, then you would lose the information of what direction it was moving in before it arrived at zero

```javascript
+"-0";				// -0
Number( "-0" );		// -0
JSON.parse( "-0" );	// -0
```

* In `non-strict mode` it's possible to override `undefined` (don't do this though)

* `void` means that expression always undefined

```javascript
processFoo();
return;

return void processFoo();
```

* Primitives are always assigned by value-copy

```javascript
var a = 2;
var b = a; // `b` is always a copy of the value in `a`
b++;
a; // 2
b; // 3
```

* Objects (arrays, functions, etc.) always create a copy of the reference on assignment or passing
* `a` and `b` in the following example are separate references to the same shared value
* `a` and `b` don't "own" the array, they both have equal peer references to the value

```javascript
var a = [1, 2, 3, 4];
var b = a;
b.push(5);
a; // [1,2,3,4]
b; // [1,2,3,4]
```

* No concept of "pointers" in JavaScript
* If `a` equals an array and `b` equals `a`
* If `b` reassigns itself to a different array, `a` will *not* be equal to the new array
* If pointers existed, reassigning `b` would reassign `a`, but it doesn't

```javascript
var a = [1,2,3];
var b = a;
a; // [1,2,3]
b; // [1,2,3]

// later
b = [4,5,6];
a; // [1,2,3]
b; // [4,5,6]
```

* What happens if box a primitive in an object wrapper (like `Number`)?
* A copy of a reference to the `Number` object will be passed, but it gets unboxed to it's primitive value
* Thus, any variable that references that `Number` object will keep it's value even if the underlying primitive value is involved in calculation

```javascript
function foo(x) {
	x = x + 1;
	x; // 3
}

var a = 2;
var b = new Number( a ); // or equivalently `Object(a)`

foo( b );
console.log( b ); // 2, not 3
```
