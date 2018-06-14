# Arrays

* An array is a linear allocation of memory in which elements are accessed by integers that are used to compute offsets
* JavaScript arrays are objects
  * When an array is created via array literal (i.e. `[]`), the first value will get the property name `0`, the second value will get the property name `1`, etc.
  * Arrays inherit from `Array.prototype` - there are more useful methods and properties in the `Array.prototype` than `Object.prototype` including the `length` property
  * Arrays are allowed to be a mixture of values (i.e. don't have to be the same type)

```javascript
// To illustrate that arrays are closely related to objects

var numbers = ['zero', 'one', 'two', 'three'];
var numbersObject = {
  '0': 'zero',
  '1': 'one',
  '2': 'two',
  '3': 'three',
};

numbers[0] // 'zero'
numbersObject[0] // 'zero'

numbers.length // 4;
numbersObject.length // undefined
```

## `length` property

* If an element is stored with a subscript (i.e. index value) that is greater than or equal to the current `length`, the `length` will increase to contain the new element
* Increasing the `length` of an array will not allocate more memory
* Decreasing the `length` of an array will remove all elements that have an index value that is greater than or equal to the new `length`

```javascript
var values = [];

values.length; // 0;
values[100] = 'one hundred';

values.length // 101;
```

## `delete` Array elements

* Executing `delete` on an array element does not lead to that index being removed but rather, the value for that index being set to `undefined`

```javascript
var numbers = ['zero', 'one', 'two', 'three'];

delete numbers[2]; // numbers is now ['zero', 'one', 'two', 'three']
```

* In order to property "delete" an element from an array, use the [`splice`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice) method

```javascript
var numbers = ['zero', 'one', 'two', 'three'];

numbers.splice(2, 1); // first number is the index to start removing elements from, the second number is the number of elements to remove

// numbers is now ['zero', 'one', 'three']
```

## `Array` validation

* The `typeof` an array is `object`

```javascript
var isArray = function (value) {
  return value && typeof value === 'object' && value.constructor === Array;
};

// if arrays were constructed in a different window or frame
var isArray = function (value) {
  return Object.prototype.toString.apply(value) === '[object Array]';
};
```

## Methods

* Can add methods to `Array.prototype`
* Since arrays are objects can add functions to individual arrays

```javascript
// Example reduce method that has been added to all Arrays

Array.method('reduce', function (f, value) {
  var i;
  for (i = 0; i < this.length; i++) {
    value = f(this[i], value);
  }
  return value;
});
```

## Initializing arrays

* Arrays are empty when initialized
* JavaScript does not have an array initialization method

```javascript
// Example array initialization method

Array.dim = function (dimension, initial) {
  var values = [], i;
  for (i = 0; i < dimension; i++) {
    values[i] = initial;
  }
  return values;
}

var arrayWith10Jaes = Array.dim(10, 'Jaes');
```

**When initializing array of arrays make sure inner arrays do not share same reference**

```javascript
// Example array of arrays initialization

Array.matrix = function (m, n, initial) {
  var matrix = [], i, j, innerArray;
  for (i = 0; i < m; i++) {
    innerArray = []; // reassign innerArray so that arrays do not share a reference
    for (j = 0; j < n; j++) {
      innerArray[j] = initial;
    }
    matrix[i] = innerArray;
  }
  return matrix;
}

// executing Array.dim(10, []) to create an array of 10 arrays is NOT good because all arrays will share same reference
```
