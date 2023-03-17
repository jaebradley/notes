# [`ToString`](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch4.md#tostring)

* When any non-`string` value is coerced to a `string`, the conversion is handled by the `ToString` `ES5` specification
* Built-in primitives have natural stringification
  * `null` => `"null"`
  * `undefined` => `"undefined"`
  * `true` => `"true"`
* Objects will use `Object.prototype.toString` unless that `toString` property is overwritten
