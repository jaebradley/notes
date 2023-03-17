# [`parseInt`](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch4.md#toboolean)

* `parseInt` operates on `string` values
* `parseInt` will try parsing the input `string` from `left` to `right` until it hits a character that it is unable to parse
  * Then it will return what it's been able to parse
* Pre-`ES5` has some quirks
  * If the first two characters are `0x`, it will interpret the input `string` as `base-16`
  * If the first character is `0`, it will interpret the input `string` as `base-8`
  * Always pass `10` as second argument for `radix` (aka base) value
  * `ES5` assumes `base-10` in all cases
  * Except when the first two characters are `0x`, in which case it's parsed as `base-16`

## `parseInt(1 / 0, 19)`

* `parseInt(1 / 0, 19)` will output `18`
* `19` represents the base
  * `base-19` is from `0-9`, `a-i` (case-insensitive)
* `1 / 0` evaluates to `Infinity`
* `Infinity` gets coerced to a `string` - `"Infinity"`
* `I` is the first character - it is `18` in `base-19`
* `n` is the next character - it has no value in `base-19`, thus the parsing finishes and `18` is returned
