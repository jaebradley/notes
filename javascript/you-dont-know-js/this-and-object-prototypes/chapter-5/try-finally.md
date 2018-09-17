# [`try`/`finally`](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch5.md#tryfinally)

* `finally` will always run right after `try` (or `catch`) finishes
* The calling code will receive anything `return`ed or `thrown` in a `try` only **after `finally` has executed**
* If the `finally` throws an exception, that will override whatever the completion value would have been in the `try` or `catch`
* If the `finally` **explicitly `return`s a value**, this will override whatever the completion value would have been in the `try` or `catch`

```javascript
function foo() {
  try {
    return 42;
  } finally {
    console.log('hello');
  }
}

// This will log 'hello' first
// Then it will log 42
console.log(foo());

function bar() {
  try {
    throw 42;
  } finally {
    console.log('hello again');
  }
}

// This will log 'hello again' first
// Then will see an Uncaught Exception: 42
console.log(bar());

function baz() {
  try {
    return 42;
  } finally {
    throw 'Baz';
  }
}

// There will be an Uncaught Exception: Baz
console.log(baz());

function jae() {
  try {
    return 42;
  } finally {
    return 'jae';
  }
}

// This will output 'jae'
console.log(jae());
```
