# [Tagged Template Literals](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch2.md#tagged-template-literals)

* The "tag" can be any expression that results in a function
* The "tag" is a special type of function invocation that doesn't need the parentheses
* First arg to tagged function expression is array of all string values in-between interpolated values
* Remaining args are all interpolated values, in-order
* Allows the ability to pre-process interpolated strings (for internationalization, localization, etc.)

```javascript
function log(strings, ...values) {
  console.log(strings);
  console.log(values);
}

const value = 'Awesome';
log`Everything is ${value}!`
// ['Everything is ', '!']
// ['Awesome']
```
