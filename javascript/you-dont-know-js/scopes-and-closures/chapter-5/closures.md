# [Closures](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch5.md#nitty-gritty)

* When a function has a reference to it's inclosing scope, that reference is called a closure
* In the following example, `foo` returns a function called `bar` that references a variable (called `a`) in `foo`'s inner scope
* Then a function instance of `bar` is created and assigned to `baz`
* Then `baz` is executed as a function and `console.log`s `'a'` since it has a closure to the inner scope of `foo` (i.e. `a`)
* Note that this occurs even when `baz` is outside the scope of `foo`

```javascript
function foo() {
  var a = 'a';

  function bar() {
    console.log(a);
  }

  return bar;
}

var baz = foo();
baz(); // a
```
