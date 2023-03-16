# [Introspection](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch5.md#inspecting-class-relationships)

* To find out if an object is a prototype of another, can use `isPrototypeOf` like `b.isPrototypeOf(c)` (does `b` appear anywhere in `c`'s prototype chain)
* To get prototype of object, `Object.getPrototypeOf(a)`
* The `__proto__` property also exists that references the internal prototype of an object (think of it as a getter / setter)

# [Delegation](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch5.md#links-as-fallbacks)

* Creating an API where prototype delegation is implied can make the API less explicit and harder to understand
* In many cases it might be easier to make the API more explicit while leaving the delegation as an internal implementation detail

```javascript
const foo = {
  someMethod: function() {
    console.log('This is some method');
  },
};

const bar = Object.create(foo);

bar.someOtherMethod = function() {
  this.someMethod();
}
```

