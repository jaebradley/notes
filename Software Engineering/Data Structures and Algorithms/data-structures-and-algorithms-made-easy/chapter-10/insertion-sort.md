# Insertion Sort

* Practically more efficient than bubble or selection sort even though all are `O(n^2)` worst case
* Stable
* In-place
* Algorithm
  * Input elements are iterated through
  * While current input element is less than previous input element and index counter is not at the start of list
    * Move previous input element to next index
    * Continue doing this until at start of list or where previous element is less than current input index
    * When this occurs, since element previously at current index was moved to next index, can replace the value at this index with current value

```javascript
function insertionSort(values) {
  for (var i = 1; i < values.length; i += 1) {
    var currentValue = values[i];
    var comparisonIndex = i;

    while (values[comparisonIndex] > currentValue && comparisonIndex > 0) {
      // Move previous input elements to next index
      values[comparisonIndex] = values[comparisonIndex - 1];
    }

    // When we get here we are either at start of list or found a value that is not greater than current value
    // Since we moved the value at this index to the next index in the while loop, we can replace it
    values[comparisonIndex] = currentValue;
  }
}
```

* Runtime
  * Worst case: `O(n^2)`
  * Best case: `O(n)`
  * Average case: `O(n^2)`
