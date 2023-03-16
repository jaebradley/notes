# [Transpilation](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch3.md#transpiling-a-generator)

* Take this generator

```javascript
function* foo() {
  var x = yield 42;
  console.log(x);
}
```

* Can transpile it by breaking down into a state machine
* In this very specific generator's case there are three states (`0`, `1`, and `2`, let's say)
  * `0` is the state where `42` gets `yield`ed out (`return`ed)
  * `1` is the state where the `yield` waits for a value to be passed to the iterator's `next` function and then assigned to the `x` variable
  * `2` is the state where the function has finished completion

```javascript
function foo() {
  function nextState(value) {
    switch (state) {
      case 0: {
        state++;

        // 42 gets yielded out
        return 42;
      }

      case 1: {
        state++;

        // x is assigned value from next
        x = value;

        // since there's not another yield, execution continues
        console.log(x);

        // gets to end of execution so return undefined
        return undefined;
      }
    }
  }

  var state = 0,
      x;

  return {
    next: function(value) {
      return {
        value: nextState(value),
        // Know that max state value is 2
        done: state === 2,
      };
    },
  };
};
```
