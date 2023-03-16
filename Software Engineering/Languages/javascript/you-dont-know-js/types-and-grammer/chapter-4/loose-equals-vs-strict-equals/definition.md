# [`Loose Equals vs. Strict Equals`](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch4.md#loose-equals-vs-strict-equals)

* Most people differentiate between `==` and `===` by saying that `==` checks values for equality while `===` checks values and types for equality
* This is incorrect - it's more accurate to say
  * `==` allows coercion when comparing equality
  * `===` does not allow coercion when comparing equality
