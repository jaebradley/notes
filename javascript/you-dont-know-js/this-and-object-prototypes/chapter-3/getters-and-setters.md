# [Getters & Setters](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch3.md#getters--setters)

* Getters are properties that call a hidden function to retrieve a value
* Setters are properties that call a hidden function to set a value
* Both properties are "accessor descriptor"s - the `value` and `writable` characteristics of these accessor descriptors are ignored
  * Instead, JavaScript considers the `set` and `get` properties (as well as `configurable` and `enumerable`)

```javascript
const foo = {
  get bar() {
    return 'bar';
  }
};

Object.defineProperty(foo, 'bar', {
  get: function() {
    return 'bar';
  },
  enumerable: true,
});
```

* Setting value of `bar` later will silently fail as there is no `set` operator - generally want both getters and setters
