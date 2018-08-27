# [Error Handling](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch4.md#synchronous-error-handling)

* Generators have synchronous error handling
* Generators can also throw errors that bubble up outside the generator

```javascript
function* main() {
  throw new Error('foobar');
}

var it = main();

try {
  it.next(); // Starting generator throws error
} catch (e) {
  console.log(e); // Foobar
}
```

* An error can be thrown *into* a generator to see if it handles the error
  * If the error is not handled by generator then it bubbles up in a `try/catch`

```javascript
var it = main();

it.next();

try {
  it.throw('foobar'); // Does generator handle error
} catch (e) {
  // No generator does not handle error
  console.log(e); // foobar
}
```
