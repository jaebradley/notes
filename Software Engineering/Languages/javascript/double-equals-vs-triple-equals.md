# [Double Equals vs. Triple Equals](https://codeburst.io/javascript-double-equals-vs-triple-equals-61d4ce5a121a)

* Triple equals (i.e `===`) means that both the types and values have to match
  * Triple equality is superior to double equality
* Type coercion occurs in double equality (`==`)
* 6 `falsy` values
  * `false`
  * `0`
  * `""`
  * `null`
  * `undefined`
  * `NaN`
* `false`, `0`, `""` will always `==` each other
  * All coerce to `false` `boolean`
* `null` and `undefined` are only `==` to each other

```javascript
null == null
// true
undefined == undefined
// true
null == undefined
// true
```

* `NaN` is not equal to anything, including itself

```javascript
NaN == null
// false
NaN == undefined
// false
NaN == NaN
// false
```
