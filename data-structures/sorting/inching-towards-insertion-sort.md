# [Inching Towards Insertion Sort](https://medium.com/basecs/inching-towards-insertion-sort-9799274430da)

* Insertion sort algorithm maintains a sublist of the initial collection that is the "sorted sublist"
* Iterates through collection and for each element, inserts the element into the correct spot in the sorted sublist
  * As we iterate, if the last sorted element is larger, shift last sorted element to next index location in array
  * If the element to sort is larger, insert element into index space vacated by previous shift

## Example

```text
// Initial array
[4, -31, 0, 99, 83, 1]

// Start with 4
[4 | -31, 0, 99, 83, 1]

// Compare -31 against 4, and move as appropriate
[-31, 4 | 0, 99, 83, 1]

// Compare 0 against 4, and then against -31 and move to appropriate spot
[-31, 0, 4 | 99, 83, 1]

// etc, etc
[-31, 0, 4, 99 | 83, 1]
```

* Worst case is `O(n^2)`
  * Have to do "full" inner loop comparison each iteration i.e. have to iterate through full sorted array until end in order to find correct insertion point
* Best case is `O(n)` for a nearly-sorted list when the number of comparisons is far fewer for inner loop
* Insertion sort is also `in-place`, `stable`, `non-recursive`, and `internal` (since it maintains all of its data in main memory)
