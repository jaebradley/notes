# [Heapify All the Things With Heap Sort](https://medium.com/basecs/heapify-all-the-things-with-heap-sort-55ee1c93af82)

* A heap is a binary tree with two special properties
  * In a max heap, a parent node has a greater value than it's child node values (obviously opposite in min heap case)
  * Tree is complete meaning that all levels, except the last are complete and the last level is complete from left to right
* Heap sort
  * Takes an unsorted collection of values (like an unsorted array of numbers)
  * Converts the collection into a heap (can be done in-place)
  * Swaps the root node (the max / min array element) with the last heap element
  * Puts the old root node in the array
  * Bubbles down swapped root node until it's in it's correct place
  * Continues process until single element left
* Remember heap does not imply total sorting, just that the parent is sorted relative to it's children
* Heaps can be represented as arrays
  * If parent is at index `0` then children are at index `1` and `2`, their children are at index `3`, `4`, and `5`, `6`, respectively, etc.
* Creating the heap takes `O(n)` since have to go through each element in array
* Bubbling down element in heap after swap is logarithmic (`O(log n)`)
  * Will need to do this swap `n-1` times (every element except initial root node)
* Thus runtime is `O(n)` + `O(n * log n)` => `O(n * log n)`
* Heap sort is unstable meaning that duplicate elements do not necessarily maintain order
  * While both heap sort and merge sort rely on tree structures, merge sort is stable
