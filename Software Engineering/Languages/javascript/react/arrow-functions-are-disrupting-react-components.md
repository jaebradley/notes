# [Arrow Functions are Disrupting React Components](https://blog.usejournal.com/arrow-functions-are-disrupting-react-components-63662d35f97b)

* Cannot rebind arrow functions (aka cannot use `bind` on them explicitly)
  * arrow functions will always be called in the context in which they're defined
* class functions are on prototype but arrow functions are on instance

```javascript
class Foo {
  constructor(name) {
    this.name = name;
  }

  function bar() {
    console.log(this.name);
  }

  foobar = () => {
    console.log(this.name);
  }
}
```

* `bar` and `foobar` in example will have same effect
* But inspecting a `Foo` instance and `Foo` itself will illustrate that
  * `foobar` is a property on the instance itself and not on the `Foo` prototype object
  * `bar` is a property on the prototype object
