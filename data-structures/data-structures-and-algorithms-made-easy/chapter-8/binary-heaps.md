# Heap

* Value of a node must be `<` (or `>`) its children (`heap property`)
* All leaves should be at height of tree or height - 1 of tree
  * Complete binary tree
    * Every level is complete except last level which is filled from left to right
* Can be represented using arrays
  * `root` is stored at index `0`, it's left child at `1`, it's right child at `2`, etc.
  * parent is at `(i - 1) / 2`
  * children are at `2 * i + 1` and `2 * i + 2`

## Heapifying

* When inserting an element, it might violate `heap property`
* So need to swap it with it's largest children (in the case of a max heap) until it satisfies the `heap property`
* Heapifying "down" the tree is sometimes called percolate down - heapyifying "up" the tree is called percolate up
* Time complexity is `O(log n)` as the worst case is starting at a root and coming down the leaf - equal to height of binary tree

## Deletion

* Generally delete operation removes root element
* Process involves replacing root element with last element and then heapifying the last element down before returning the previous root element

## Insertion

* Insert new element at end of heap
* And then heapify the element up the tree if necessary

## Heap Sort

* Insert all elements from unsorted array, then remove from root of heap until heap is empty
* Heap array can be sorted in-place
  * Once heap has been created and elements have been added, exchange first element with last element and decrement array size
  * Then heapify new root
  * Continue process until array size is 0
* Time complexity of insert and delete is `O(log n)` (`n` is number of items in heap) so time complexity of heap sort is `O(n log n)`
