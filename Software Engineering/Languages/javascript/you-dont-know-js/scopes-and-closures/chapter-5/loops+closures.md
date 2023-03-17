# [Loops + Closures](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch5.md#loops--closure)

Why does

```javascript
for (var i = 1; i < 6; i++) {
  setTimeout(function timer() {
    console.log(i);
  }, i * 1000);
}
```

output `6`, five times, every second?

* This is because `i` is a variable in the global scope
* When the `setTimeout` callback functions execute, the `i` they reference is the value when the loop terminates, `6`
* What we really want is for each loop to have it's value of `i` match the value at iteration time

```javascript
for (var i = 1; i < 6; i++) {
  (function() {
    var j = i;
    setTimeout(function timer() {
      console.log(j);
    }, j * 1000);
  })();
}
```

* The inner function creates its own scope and of course `j` is the value of `i` at each iteration
* Thus, when the callbacks are executed, they reference the `j` variable from their scope (i.e. the `j` that is part of their closure)

## Using Block Scoping to solve this problem

* Remember how `let`s are block-scoped?
* We can solve the `for` loop by using `let` in the block
  * When the callback executes, it references the `j` in it's closure, which is block-scoped to the value of `i` at that specific iteration

```javascript
for (var i = 1; i < 6; i++) {
  let j = i;
  setTimeout(function timer() {
    console.log(j);
  }, j * 1000);
}
```

* We can also use a `let` in the `for` loop directly
  * This means that `i` will be declared *each* iteration

```javascript
for (let i = 1; i < 6; i++) {
  setTimeout(function timer() {
    console.log(i);
  }, i * 1000);
}
```
