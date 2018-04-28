# Pivoting To Understand Quicksort Part 2

## Performance when array is nearly sorted

```text
[2, 1, 6, 7, 13, 15, 16]
                     P

[2, 1, 6, 7, 13, 15, 16]
                 P

[2, 1, 6, 7, 13, 15, 16]
             P

// keep doing this will run n^2 operations
// since almost all elements are smaller than pivot
```

* Average runtime for unsorted list and a partition that is close to median is `O(n log n)`
* Average runtime for sorted or nearly-sorted list, or a partition that is far from median is `O(n^2)`
* Quicksort does require *some* extra space
  * Recursive algorithms allocate `left` and `right` pointers as it partitions and sorts down to single-element lists
  * Requires `O(log^2 n)` extra space
  * Most people think of this as an `in-place` algorithm
  * Because it can sort without creating a new list, it's an `internal` sorting algorithm
* Quicksort is **unstable** meaning that it won't be guaranteed to preserve order of elements
  * two elements that are exactly the same in the unsorted array could show up in reversed order in the sorted one
* Further quicksort optimizations
  * Running each partition in parallel
    * `left` and `right` don't need to know about each other, so they can be executed concurrently, taking 1/2 the time
  * Look at last few elements in list - select median element from amongst them
