# [Implicit Binding](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch2.md#implicit-binding)

* If the call site has a context object (e.g. the context object has a property that references a function that uses `this`) then `this` will reference that context object

```javascript
function foo() {
  console.log(this.a);
}

var someObject = {
  a: 2,
  foo: foo,
};

someObject.foo(); // 2
```

## When implicit bindings lose their context

* Remember that when a function is "wrapped" by it's context object via a property, for example, the context object really has a reference to that object
  * In the following example, `bar` is assigned the reference for `foo`
  * Since `bar` is a plain, undecorated call-site, "default binding" will apply
  * So the call site for `bar` is the global scope, so `this.a` will reference the global object's `a` property, which is `"some global a"`

```javascript
function foo() {
  console.log(this.a);
}

var someObject = {
  a: 2,
  foo: foo,
};

var a = 'some global a';

bar = someObject.foo; // this is a function reference
bar(); // some global a
```

* This often happens with callback functions

```javascript
function foo() {
  console.log(this.a);
}

function callback(fn) {
  fn();
}

var someObject = {
  a: 2,
  foo: foo,
};

var a = 'some global a';

callback(someObject.foo); // some global a
```
