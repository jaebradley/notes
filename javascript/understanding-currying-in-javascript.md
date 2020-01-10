# [Understanding Currying in JavaScript](https://blog.bitsrc.io/understanding-currying-in-javascript-ceb2188c339)

* Currying is a process in functional programming in which a function with multiple arguments is transformed into a sequence of nesting functions
  * Each function in sequence of nested functions takes a single argument

```javascript
// Imagine if a multiply function that takes three arguments was curried - it would look something like

function multiply(a) {
  return (b) => {
    return (c) => {
      return a * b * c;
    }
  }
}
```

* The `multiply` function is a series of functions instead of a single function
  * It keeps all three values by taking advantage of closure
  * Each function in the series of functions is a "specialized" function in that it has prior information about the values that were inputted into the previous function(s)
* Partial application is simply when a function is transformed into another function with smaller arity (i.e. it takes fewer arguments)
  * `f(x, y, z) => g(y, z) * x` is transformed into `f(x) => (y, z) => g(y, z) * x`
* Can use currying to avoid calling a function with same argument
  * Consider a `volume` function and let's assume all your cylinders have the same fixed height
    * If you curry the `volume` function where the first argument is the `height` value then you can use the curried function like

```javascript
const volumeWith100CylinderHeight = curriedVolume(100);
volumeWith100CylinderHeight(someWidth)(someLength);
```
