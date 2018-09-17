# [Expression Side Effects](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch5.md#expression-side-effects)

* `b = a++` does two things
  * First, it returns the current value of `a` (in this case, assigning that value to `b`)
  * Second, it increments the current value of `a`
  * Can also do `b = (a++, a)` if you want `b` and `a` to share the same value
    * The second argument, `a`, means that it gets evaluated after `a++` completes (i.e. after `a` is incremented) so when `a` is returned, `b` and `a` share the same value
* Conversely, `b = ++a` reverses the order of operations (i.e. increment first, return second)
* Can combine assignment side effect like

```javascript
// This
function vowels(str) {
  var matches;

  if (str) {
    matches = str.match(/[aeiou]/g);

    if (matches) {
      return matches;
    }
  }
}

// Can be simplified to
function vowels(str) {
  var matches;

  if (str && (matches = str.match(/[aeiou/]/g))) {
    return matches;
  }
}

// Benefit is that it combines the two boolean if statements together
// Note the fact that the matches variable assignment is in parentheses - this is due to operator precedence
```
