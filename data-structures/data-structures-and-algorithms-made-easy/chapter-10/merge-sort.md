# Merge Sort

* Used for sorting a linked list
* Stable
* Divides list into two parts, then applies merge sorting to those two parts, etc.
* These lists are then merged back together by scanning and comparing the sublists (i.e. given two sorted lists, interleaving them to get one sorted list)

```javascript
// https://hackernoon.com/programming-with-js-merge-sort-deb677b777c0
// Split the array into halves and merge them recursively
function mergeSort (arr) {
  if (arr.length === 1) {
    // return once we hit an array with a single item
    return arr
  }

  const middle = Math.floor(arr.length / 2) // get the middle item of the array rounded down
  const left = arr.slice(0, middle) // items on the left side
  const right = arr.slice(middle) // items on the right side

  return merge(
    mergeSort(left),
    mergeSort(right)
  )
}

// compare the arrays item by item and return the concatenated result
function merge (left, right) {
  let result = []
  let indexLeft = 0
  let indexRight = 0

  while (indexLeft < left.length && indexRight < right.length) {
    if (left[indexLeft] < right[indexRight]) {
      result.push(left[indexLeft])
      indexLeft++
    } else {
      result.push(right[indexRight])
      indexRight++
    }
  }

  return result.concat(left.slice(indexLeft)).concat(right.slice(indexRight))
}
```

* Runtime
  * Worst case: `O(n x log n)`
  * Best case : `O(n x log n)`
  * Average case: `O(n x log n)`
  * Worst case space: `O(n)`
