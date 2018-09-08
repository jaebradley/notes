# [`ToNumber`](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch4.md#tonumber)

* `true` => `1`
* `false` => `0`
* `undefined` => `NaN`
* `null` => `0`
* For `string`s, it's essentially the same as the numeric literal
  * If `ToNumber` fails, the result is `NaN`
* `ToNumber` uses the `ToPrimitive` abstract operation to operate on `object`s
  * The `object` is first converted to it's primitive equivalent
  * And then it's converted to a `number` using the aforementioned primitive conversions
