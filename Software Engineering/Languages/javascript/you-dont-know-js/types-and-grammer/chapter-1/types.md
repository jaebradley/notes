# [Types](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch1.md#chapter-1-types)

* 7 Types
  * `null`
  * `undefined`
  * `string`
  * `number`
  * `object`
  * `boolean`
  * `symbol`

* `typeof null` is `object`
  * To check `null` gotta do `!value && typeof value === 'object'`
* `typeof function() === 'function'`
  * `function` is "subtype" of `object`
  * `function` is a "callable object"
    * `object` that has an internal `call` property

```javascript
var a;

a; // undefined
b; // ReferenceError
```

* `typeof` operator returns `undefined` for "undeclared" variable

```javascript
var a;

typeof a; // undefined
typeof b; // undefined
```
