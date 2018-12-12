# [`for..of` Loop](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch2.md#forof-loops)

* `for..of` loops over values produced by an iterator
  * Built-ins that are by default, iterable
    * Arrays, Strings, Generators, Collections
  * Objects are not by default, iterable and are not suitable for `for..of` looping because they do not have default iterable
  * If `for..of` loop is terminated (`break`, `continue`, `return`) the iterator's `return` function is called to perform any clean up tasks
* `for..in` will loop over keys and indices and `for..of` will loop over values

```javascript
const values = ['a', 'b', 'c', 'd'];

for (let index in values) {
  console.log(index);
  // 0, 1, 2, 3
}

for (let value of values) {
  console.log(value);
  // 'a', 'b', 'c', 'd'
}
```
