# [There is no such thing as an `else if`](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch5.md#else-if-and-optional-blocks)

* An `else if` clause is just a combination of an `if` statement and an `else` statement
* An `else if` gets parsed to

```javascript
if (a) {
  // do something
} else {
  if (b) {
    // now in "else if"
  } else {

  }
}
```

* This is because `else` and `if` both take optional blocks if they only contain a single statement
  * For example, `if (a) doSomething(a);`
