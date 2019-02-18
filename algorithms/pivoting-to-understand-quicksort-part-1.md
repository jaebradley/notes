# [Pivoting To Understand Quicksort: Part 1](https://medium.com/basecs/pivoting-to-understand-quicksort-part-1-75178dfb9313)

* The idea behind quicksort is relative order
* Pick some "pivot" value to relatively order the remaining values around
* Values to the left of this pivot value are less than the pivot value and then values to the right of the pivot value are greater than the pivot value
* How this is implemented at a high-level is that
  * Some arbitrary element is picked as the pivot element (let's say it's the last element)
  * Element at start of values and element at end of values (excluding pivot element) are used as the initial "left" and "right" boundaries of ordering
  * If value at left boundary is less than pivot element, move the left boundary up an index
  * If the value at the right boundary is greater than the pivot element, move the right boundary down an index
  * If the value at the left boundary is greater than the pivot element AND the right boundary value is less than the pivot element, swap the values at the indices
    * After swap, increment both indices
  * Once, index of left boundary passes index of right boundary, the relative ordering is done and the value at the left index is swapped with current pivot (last element)
  * Then this relative sorting process is continued on both the left and right partitions, where they pick their respective pivots
* You can see how the partitioning breaks the sorting process into smaller and smaller subproblems to the point where relatively sorting one or two elements is the same as absolutely sorting them (with respect to each other)
