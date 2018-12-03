# [Built-In Objects](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch3.md#built-in-objects)

* `String`, `Number`, `Boolean`, etc. are functions
* These can be used as constructors to produce newly constructed object of the specified sub-type

```javascript
const someString = 'foobar';
typeof someString // string
someString instanceof String // false

const someStringObj = new String('foobar');
typeof someStringObj // object
someString instanceof String // true
```

* `'foobar'` is a primitive literal and immutable
* In order to perform operations on it, a `String` object is needed
* JavaScript coerces `string` primitives to a `String` object when necessary
* Simpler literal form is usually preferred
