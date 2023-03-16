# [Operator Precedence](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch5.md#operator-precedence)

* `b = (a++, a)` is interpreted as
  * `a++` is executed first (remember this `return`s `a` first, then increments `a`)
  * then `a` is returned (and this would be the newly incremented `a`)
* `b = a++, a` is interpreted as
  * `b = a++` then `a` (or `(b = a++), a`)
  * This means that `b` will be assigned `a`'s value (before increment)
  * Then `a` will be evaluated (with it's incremented value)
* This occurs because `,`, the `statement-series` operator has the lowest precedence
* The reason for the parentheses here `str && (matches = str.match(/[aeiou]/g))` is because `&&` has greater precedence than `=`
  * Without the parentheses, this would be interpreted like `(str && matches) = str.match(/[aeiou]/g)`
  * This would ultimately error since `str && matches` isn't going to result in a variable and cannot be the `LHS` assignment

## `&&` vs. `||` precedence

* `&&` will always have precedence over `||`
* Even if the `&&` is specified after the `||`

```javascript
// This is true
true || false && false;

// This is false
(true || false) && false;

// This is true
// This implies that the `false && false` will be executed first
true || (false && false)
```

## `?` precedence

* `&&` has more precedence than `||` which has more precedence than `?`

```javascript
// This
a && b || c ? c || b ? a : c && b : a

// Gets processed like
((a && b) || c) ? (c || b) ? a : (c && b) : a
```

## Associativity

* `&&` and `||` are left-associative
  * This means that `a && b && c` => `(a && b) && c`
* If `&&` was right-associative (aka `a && b && c` => `a && (b && c)`) this **would not imply that `c` could get executed first**
  * Associativity is about grouping and **not about execution order**
  * `a` would get evaluated, then `b`, then `c`
* `?` is right-associative

```javascript
// This
a ? b : c ? d : e

// Is equivalent to
a ? b : (c ? d : e)

// And not equivalent to
(a ? b : c) ? d : e
```

This makes a difference

```javascript
// Consider
// This should output false
true ? false : true ? false : true

// This will also output false
true ? false : (true ? false : true)

// This will output true
(true ? false : true) ? false : true
```

* `=` is also right-associative
* That's why `a = b = c = 42` is processed by first evaluating the `c` variable assignment, then the `b` variable assignment, then the `a` variable assignment
  * Basically, it's `a = (b = (c = 42))`

## Mini Quiz

```javascript
var a = 42;
var b = "foo";
var c = false;

var d = a && b || c ? c || b ? a : c && b : a;

// This can be broken down like
(( a && b ) || c) ?
  (
    (c || b) ?
      a : (c && b)
  ) : a

// Let's replace values
(( 42 && "foo") || false) ?
  (
    (false || "foo") ?
      42 : (false && "foo")
  ) : 42

// Let's evaluate some more
true ?
  (
    true ? 42 : false
  ) : 42

// And some more
true ? 42 : 42

// 42
```
