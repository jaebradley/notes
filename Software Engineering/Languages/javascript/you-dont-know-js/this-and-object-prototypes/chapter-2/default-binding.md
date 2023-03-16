# [Default Binding](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch2.md#default-binding)

* As the name suggests, default binding, is, well, the default binding
* In the following case, the call-site for `foo` is the global scope
  * This means that in `foo`, `this` will point at the global object
  * In the global object, `a` is declared with the value `2`
  * Thus, when `foo` is executed, it outputs `2`

```javascript
function foo() {
  console.log(this.a);
}

var a = 2;

foo(); // 2
```

* If `strict mode` is enabled in `foo`, then `foo` will throw a `TypeError` because `this` is not eligible for default binding
* However, if `strict mode` is **not** enabled in `foo`, then the `strict mode` state of `foo`'s call site is irrelevant
