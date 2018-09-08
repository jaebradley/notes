# [`JSON.stringify`](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch4.md#json-stringification)

* Any `JSON`-safe value can be stringified using `JSON.stringify`
  * `undefined`, `function`, `symbol`, and `object` with circular references are not `JSON`-safe
  * In an array, these values will be repalced with `null`
  * In an object, this property will be excluded
* If an `object` has a `toJSON` method, that method will be called first
  * `toJSON` should return a `JSON`-safe representation of the `object`
  * `toJSON` should not be a `string`
  * `toJSON` should be interpreted as `to a JSON-safe value suitable for stringification` vs. `to a JSON string`
* `JSON.stringify` takes two aditional arguments
  * The first is a `replacer`
    * If it's an `array`, it'll whitelist only the keys specified in the `array`
    * If it's a `function`, it'll use the `function` to whitelist only the non-`undefined` valuse returned by the `function`
  * The second is a `spaces` count
    * It'll indent the output using the passed in number of spaces
