# [Implementing Heaps in JavaScript](https://blog.bitsrc.io/implementing-heaps-in-javascript-c3fbf1cb2e65)

* Heap has property where it represents a complete tree where every level, except (possibly) the last one is completely filled and nodes are as left as possible
* Heap is used to get the highest priority element at any point in time
* Parent node is always greater than / less than it's children
* Used to get  min / max element in `O(1)` time
* Heaps stored as arrays in memory

```bash
         10
       /    \
      23    36
     /  \  /  \
    32  38 45  57

[10, 23, 26, 32, 38, 45, 47]
```

* Relationship between parent and children is that given parent index `i`, the children indices are at `2 x i + 1` and `2 x i + 2`
  * Parent indices are at `Math.floor(childIndex / 2)`
* When inserting a new value
  * Insert value at "end" of heap
  * Check parent node - if parent is smaller than new value (in max heap) then swap
  * Keep doing this until find parent node that is larger than current value
  * Process is called "heapifying" or "heapifying up"
  * Runtime is `O(log n)` (`n` is total number of elements)
* When removing top value
  * Initially put last value in heap at top of heap
  * Swap this value with the smallest of the child node values until no remaining child nodes or no smaller child nodes
  * Process is called "heapifying down"
  * RUntime should also be `O(log n)` as halving total number of elements to consider on each "heapify down" step

