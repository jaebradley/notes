# Pivoting To Understand Quicksort Part 1

* Chooses pivot point in a collection of elements
  * Partitions collection around pivot so elements smaller than pivot are moved before and elements larger than pivot are moved after
  * This partitioning and ordering is recursively executed for each partitioned sublist (i.e. the collection that is left and right of the pivot)
  * Generally, element chosen as pivot is arbitrary (though can impact performance) - often last element is chosen
* Even though at each step (before the last one), the list isn't completely sorted, the order of the items relative to the pivot is correct

## Example

```text
[9, 12, 9, 2, 17, 1, 6]

// 6 is pivot
// So all indices left of pivot (6) must be less than (or equal)
// and all indices right of pivot must be greater than

[1, 2, 6, 9, 12, 9, 17]

// Collection is still not sorted correctly but it is sorted in relation to pivot

// Left sublist
[1, 2]

// 2 is pivot
// 1 is in correct place

// Right sublist
[9, 12, 9, 17]

// 17 is pivot
// Correctly sorted with respect to pivot

// Left sublist (of previous "right" sublist)
[9, 12, 9]

// 9 is pivot
[9, 9, 12]

// Putting it all together
[1, 2] + [6] + [9, 9, 12] + [17]
[1, 2, 6, 9, 9, 12, 17]
```

## The Algorithm

* Quicksort sorts elements in-place by swapping elements
  * Choose a pivot (let's take the highest-index item)
  * Create a left reference pointing to element at the lowest index
  * Create a right reference pointing to the element at highest index that's not the pivot
  * While the left reference is less than the pivot, move the pointer one element to the right
  * While the right reference is greater than the pivot move the pointer one element to the left
  * If left reference is greater than pivot **and** right reference is smaller than pivot, swap elements at the two references
  * Keep doing this until left reference is at an index >= right reference index
  * Swap pivot with element at **left** reference

## Another Example

```text
[9, 5, 2, 6, 1, 11, 3]
 L               R  P

// Left is not less than pivot so reference remains
// Right is greater than pivot so reference will move to left

[9, 5, 2, 6, 1, 11, 3]
 L           R      P

// Left is still not less than pivot
// Right is not greater than pivot
// So swap values at index

[1, 5, 2, 6, 9, 11, 3]
 L           R      P

// Can now move references again, rinse, repeat
[1, 5, 2, 6, 9, 11, 3]
    L     R         P
```
