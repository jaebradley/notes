# Quicksort

* Algorithm
  * One or no elements in the array, then return
  * Pick element in array to serve as pivot (like the left-most element in array)
  * Split array into two parts - one where elements are larger than pivot and one where elements are smaller than pivot
    * The partitioning works by starting from beginning of list and end of list
    * Scan from start of array until find an entry that is greater than or equal to pivot value
    * Scan from end of array until find an entry that is less than pivot value
    * At this point the left value should be on the right partition and the right value should be on the left partition
    * These values are swapped
    * Scanning continues in this fashion until the left index and right index cross
    * When indices cross, swap pivot value (which is at first index) with right-most value of left subarray
  * Recursively call quicksort on both halves
* Runtime
  * Worst-case: `O(n^2)`
  * Best case: `O(n x log n)`
  * Average case: `O(n x log n)`
  * Worst case space: `O(1)`

```javascript
// https://medium.com/@Charles_Stover/implementing-quicksort-in-javascript-8044a8e2bf39
const quickSort = (
  unsortedArray,
  comparator = defaultComparator
) => {

  // Create a sortable array to return.
  const sortedArray = [ ...unsortedArray ];

  // Recursively sort sub-arrays.
  const recursiveSort = (start, end) => {

    // If this sub-array is empty, it's sorted.
    if (end - start < 1) {
      return;
    }

    const pivotValue = sortedArray[end];
    let splitIndex = start;
    for (let i = start; i < end; i++) {
      const sort = comparator(sortedArray[i], pivotValue);

      // This value is less than the pivot value.
      if (sort === -1) {

        // If the element just to the right of the split index,
        //   isn't this element, swap them.
        if (splitIndex !== i) {
          const temp = sortedArray[splitIndex];
          sortedArray[splitIndex] = sortedArray[i];
          sortedArray[i] = temp;
        }

        // Move the split index to the right by one,
        //   denoting an increase in the less-than sub-array size.
        splitIndex++;
      }

      // Leave values that are greater than or equal to
      //   the pivot value where they are.
    }

    // Move the pivot value to between the split.
    sortedArray[end] = sortedArray[splitIndex];
    sortedArray[splitIndex] = pivotValue;

    // Recursively sort the less-than and greater-than arrays.
    recursiveSort(start, splitIndex - 1);
    recursiveSort(splitIndex + 1, end);
  };

  // Sort the entire array.
  recursiveSort(0, unsortedArray.length - 1);
  return sortedArray;
};
```
