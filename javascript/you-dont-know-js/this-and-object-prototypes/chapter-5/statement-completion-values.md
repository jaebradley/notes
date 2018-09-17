# [Statement Completion Values](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch5.md#statement-completion-values)

* The completion value of a block is the value of the last statement / expression in the block
  * In other words, the completion value of a block is like an implicit return of the last statement value in the block

```javascript
var b;

if (true) {
  b = 10 + 20;
}

// this will return 30
```

* However, can't assign the completion value of a block to a variable

```javascript
var b;

var a = if (true) {
  b = 10 + 20;
}
```

* `ES7` proposal to use `do` block like

```javascript
var a, b;

a = do {
  if (true) {
    b = 10 + 20;
  }
}

// a is 30;
```
