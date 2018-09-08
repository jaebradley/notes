# [`ToBoolean`](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch4.md#toboolean)

* There are `7` `falsy` values
  * `+0`
  * `-0`
  * `false`
  * `""` (empty string)
  * `null`
  * `undefined`
  * `NaN`
* If something is not `falsy`, it's `truthy`
* That's why `!!new Boolean(false)` is `true` - the `Boolean` object is *not* falsy
