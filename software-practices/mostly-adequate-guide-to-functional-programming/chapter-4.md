# Chapter 4: Currying

* When you curry a function (usually the function has multiple arguments), it means that you call that function one argument at a time, until all function arguments have been defined and the function evaluates
  * Each time an argument is applied, a new function is returned with the previous arguments applied

```javascript
const add = (x, y) => x + y

// note how the curriedAdd takes x
// then returns a function that takes y
// which only then returns a result with x and y (via closure)
const curriedAdd = x => y => x + y
```

* By currying, you can "pre-load" a function with an argument (or many arguments)
