# [JavaScript Equality Comparison Algorithm](https://medium.com/better-programming/javascript-equality-comparison-algorithm-d827917fec6a)

* Comparison between equivalent types
  * If both values are `undefined`, return `true`
  * If both values are `null`, return `true`
  * If both values are booleans, return if values are same
  * If both values are an object, return `true` if object references are equivalent
  * If both values are a number
    * If first value or second value `NaN`, return `false`
* Comparison values of different types
  * `null == undefined` and `undefined == null` return `true`
  * First value is a string and second value is a number
    * String value is converted to a numeric value, and then they are compared
  * First value is a boolean and second value is a number
    * Boolean is converted to a number and compared with the number
    * `true` => `1` and `false` => `0`
  * First value is an object, second value is a string
    * Generally, `toString` function will be used for an object
      * More specific definition is that the object will use `ToPrimitive` which will call `toString` _and_ `valueOf`
    * Default value is `[object Object]`
