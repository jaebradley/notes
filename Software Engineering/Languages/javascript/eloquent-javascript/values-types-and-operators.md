# Values, Types And Operators

## Numbers

* JavaScript uses 64 bits to represent numbers
  * Given 64 binary digits, you can represent `2^64` values (~`18 quintillion` or `18` followed by `18` zeros)
  * However, JavaScript uses some of the bits to represent sign, or decimal places
  * So max whole number is around `9 quadrillion` (only `15` zeros)
* Can represent numbers using scientific notation (`2.998e8`)
* `NaN` is of type `number` and is only value in JavaScript that is not equal to itself
  * Supposed to represent result of nonsensical operation, and isn't equal to the result of any other nonsensical operation
  * Further numeric operations using `NaN` produce `NaN`

## String

* 16 bits (describes up to `2^16` characters)
* JavaScript uses `Unicode` standard
  * However, `Unicode` standard describes more than `2^16` characters
  * JavaScript uses `2` character positions to represent these `Unicode` characters (like emojis)
* If two backticks follow each other (`\\`), they will collapse
  * `"A newline character is written like \"\\n\"."` => `A newline character is written like "\n".`

## Automatic Type Conversion

* When using the `==` comparator, JavScript tries to coerce values to the same type
  * For `null` or `undefined`, they only `==` `null` or `undefined`
  * `null == undefined` is `true`
  * `null == 0` is `false`
* When using the `===` comparator, no type conversion happens

## Short-Circuiting of Logical Operators

* `||` returns value to left when it can be converted to `true` and the right value otherwise
  * `null || 'foo'` => `null`
  * `'foo' || 'bar'` => `foo`
* `&&` returns the value to left when it can be converted to `false` and right value otherwise
* Right part of operators are only evaluated when necessary
