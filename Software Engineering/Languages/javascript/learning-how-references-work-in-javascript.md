# [Learning How References Work in JavaScript](https://medium.com/@naveenkarippai/learning-how-references-work-in-javascript-a066a4e15600)

* A value's `typeof` operator determines how the value is assigned to a variable
* If the `typeof` operator indicates a scalar primitive, then the value is stored with assign-by-value
* If the `typeof` operator indicates a compound value (like an `Object` or an `Array`), the value is stored with assign-by-reference
* Scalar primitives are immutable while compound values are mutable
* References in JS point at values **and NOT at other variables or references**

## Examples

### Mutating a shared primitive value

```javascript
var foo = 7;
var bar = foo; // bar also has the value 7
bar++; // bar is 8, and foo is 7
```

### Mutating a shared complex value

* In this example, `foo` and `bar` both share a reference to the underlying `Array` value

```javascript
var foo = [1, 2, 3];
var bar = foo; // bar and foo are both [1, 2, 3]
bar.push(4); // bar and foo are both [1, 2, 3, 4]
```

### Complex value variable reassignment

* In this example, `foo` and `bar` initially share the same reference, but when `bar` gets reassigned to a different `Array` reference, `foo` doesn't get reassigned
* This is because variables have references to values and not other references or other variables

```javascript
var foo = [1, 2, 3];
var bar = foo;
bar = [4, 5, 6]; // bar is [4, 5, 6] but foo is [1, 2, 3]
```

### Mutating array without reassignment

* If reassigning variable only leads to assignment to a different reference, how to mutate array reference without reassignment?
* Assigning underlying `length` property of shared array reference to `0` effectively "clears" the array
* Can then `push` whatever values you'd want

```javascript
var foo = [1, 2, 3];
var bar = foo;
bar.length = 0;
bar.push(4, 5, 6); // foo and bar should both be [4, 5, 6]
```
