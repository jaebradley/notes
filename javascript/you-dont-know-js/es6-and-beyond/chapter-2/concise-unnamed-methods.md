# [Concise Methods](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch2.md#concisely-unnamed)

* It's important that the function name `bar` is defined
* This way, we can call the function recursively, if we need to
  * Could do `foo.bar()` to execute the recursion, but this has drawbacks as well, including the assumption that the object is always going to be called `foo`
  * Could also use `this` like `this.bar()` but this runs into `bind`ing issues inside event handlers
    * This can be solved by `bind`ing like `foo.bar.bind(foo)` but it's not a super-appealing option

```javascript
const foo = {
  bar: function bar() {
    return bar();
  }
}
```

* However, when defining `bar` using the concise syntax, it requires the name for recursion, because without it, the function is an anonymous function

```javascript
const foo = {
  bar() {
    return bar(); // ReferenceError
  }

  baz: function baz() {
    return baz();
  }
}
```
