# [Here's Why Mapping A Constructed Array Doesn't Work In JavaScript](https://itnext.io/heres-why-mapping-a-constructed-array-doesn-t-work-in-javascript-f1195138615a)

**Generate an array of numbers from `0` to `99`**

* Could do

```javascript
const arr = [];
for (let i = 0; i < 100; i++) {
  arr[i] = i;
}
```

...but it's not really functional

* So then what about

```javascript
// instantiate an empty array of length 100 and map the index of each element
const arr = Array(100).map((_, i) => i);
console.log(arr[0]); // undefined
```

## JavaScript Arrays are Objects

* The array indices are keys
* So `['a', 'b', 'c']` is essentially equivalent to `{ 0: 'a', 1: 'b', 2: 'c', length: 3 }`
* `Array(1--)` creates an object with the `length` property equal to `99` but there are no index keys
* `map`, `reduce`, `filter`, etc. iterate over the index keys of the array from `0` to `length`, but the callback is only executed *if the key exists on the object*.

## Solution

```javascript
const arr = [...Array(100)].map((_, i) => i);
arr[0]; // 0
```

* Spreading the `Array(100)` results in

```javascript
{
  0: undefined,
  1: undefined,
  ...
  99: undefined,
  length: 100,
}
```

* This is because the spread operator loops through any iterable from `0` to `length` and creates a new index key in the enclosin array with the value returned from the spread array at the current index
* The mapped values are `undefined` because JavaScript returns `undefined` if it does not have an index key for that value
