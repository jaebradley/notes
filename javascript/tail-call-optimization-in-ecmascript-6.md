# [Tail Call Optimization In ECMAScript 6](https://2ality.com/2015/06/tail-call-optimization.html)

* Even though tail call optimization is part of language spec, it isn't supported by many engines

```javascript
function id(x) {
  return x;
}

function f(a) {
  let b = a + 1;
  return id(b);
}

console.log(f(2));
```

* Initially global variables `id` and `f` on stack
* A stack frame is the block of stack entries that has the state of the current scope (like local variables and parameters)
* When `console.log` is called, the location to return to is saved on stack
* Then a new stack frame is created for the call to `f`
  * This stack frame has the values `a` for the parameter and `b` for the variable in the function body
* When `id` is called inside `f`, a new stack frame is added that contains the return address to the line in `f` and `id`'s parameter
* When `id` finishes evaluating, the result is returned, and `id`'s stack frame is removed so only global stack frame and `f`'s stack frame exist
  * `f` returns the value returned by `id`'s stack frame and `f`'s stack frame is removed
* `console.log` receives the value from the `f` stack frame and outputs it
* When `id` finishes, the value basically gets passed back to `f`'s caller (i.e. the global stack frame)
  * So `f`'s variables are not needed anymore and its stack frame can be removed before making the call to `id`
  * So the return address given to `id` can just be `f`'s return address (inside the `console.log`)
  * So now, there is zero stack growth - but this can only be done if a function call is a tail call (i.e. it's the last action in a function)

## Tail Calls in Expressions / Statements

### The OR Operator

```javascript
const a = () => f() || g()
```

* `f()` is not in tail position but `g()` is in tail position

```javascript
const a = () => {
  let result = f(); // not a tail call
  if (result) {
    return result;
  }

  return g(); // tail call
}
```

* Since the result of the logical OR operator depends on the result of `f()`, `f()` is not in a tail position (since caller does something with it other than returning it)
* Same logic applies to logical AND operator - `g()` is tail position for `f() && g()`

### Statements

* Only `then` or `else` clause in `if` can contain tail call
* `switch` - body can contain tail calls
* `try-catch` - only `catch` clause can contain tail call
* `try-catch-finally` - only `finally` clause can contain tail call
* For all other non-compound statements, only `return` can contain a tail call
* Solo function calls are never in tail position

```javascript
function foo() {
  bar();
}
```

* `bar` is not a tail call because there is an implicit `return undefined` at the end of `foo`

## Tail-Recursive Functions

* A function is `tail-recursive` if the main recursive calls it makes are in tail positions

```javascript
function factorial(x) {
    if (x <= 0) {
        return 1;
    } else {
        return x * factorial(x-1);
    }
}
```

* Not tail-recursive because `factorial` still needs to multiply `x` with the result of the recursive call to `factorial`

```javascript
function factorial(n) {
    return facRec(n, 1);
}
function facRec(x, acc) {
    if (x <= 1) {
        return acc;
    } else {
        return facRec(x-1, x*acc);
    }
}
```

* Now it's tail-recursive since the `return` is "pure"