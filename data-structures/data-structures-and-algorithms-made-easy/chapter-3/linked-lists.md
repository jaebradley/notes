# Linked List

* Successive elements are connected by pointers
* The last element points to `NULL`
* Can grow or shrink in size
* Access time for an individual element is `O(n)` in the worst case

## Arrays

* A block of memory that is allocated to store elements
  * This can lead to performance improvements for modern CPU caching strategies since an array element will be near it's neighbors
* An index is used to access elements in constant time
* Elements are accessed via memory address which is computed using the address of the array in memory and the index
* Must allocate memory up-front
  * Memory is wasted for unused indices
  * Arrays are static (it's size is defined up front)
* Inserting elements at the beginning of the array means shifting all other elements which is expensive
* Can use dynamic arrays that solve this problem - when initial array reaches some size, a new array is created with double the size or something, and then the existing values are copied over

## Insertion

* Generally, always three cases to think about
  * Inserting new node at beginning of list (i.e. before the current head of the list)
  * Inserting at the end of the list
  * Inserting between two elements in the list
* Inserting at beginning
  * Create new node
  * Point node's `next` at existing `head` node
  * Point `head` reference to newly created node
* Inserting at the end
  * Create new node
  * Point node's `next` at `NULL`
  * Iterate through list until at current end node
  * Point current end node's `next` at newly created node
* Inserting in middle
  * Create new node
  * Iterate through list until at node where inserted node will be it's `next` reference
  * New node's `next` will point to current node's `next`
  * Current node's `next` then gets pointed to newly created node
  * In doubly-linked case, newly created node's `next` and `previous` are assigned to current node's `next` and current node, respectively

## Deletion

* Deleting at end of list
  * Iterate until at penultimate node (use two pointers, one that will be at end and one that will be right behind it)
  * Point node's `next` to `NULL` and remove the last node from memory
