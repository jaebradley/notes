# Problems

## Give an algorithm for finding the kth smallest element in a min-heap

### Solution #1

* Perform `k` deletions on the min heap
* Runtime is `O(k x log(n))`
* Deletion operation is `O(log(n))` and you do that `k` times

### Solution #2

* Get minimum element in min heap
* Create a second heap
* Add minimum element to this heap
* Remove the top element of the second heap
* Add the children of the top element to the heap
* Do this process `k` times - at the end, you should have the `kth` smallest element
* Runtime is `O(k x log(k))`
* This runtime matters when diff between `n` and `k` is large

```javascript
function findSmallestElement(k, heap) {
  var secondHeap = createHeap();
  var currentMinimumElement = heap.getMinimum();
  secondHeap.insert(currentMinimumElement);
  var count = 1;

  while (count < k) {
    var currentElement = secondHeap.deleteMinimum();
    secondHeap.insert(currentElement.left);
    secondHeap.insert(currentElement.right);
    count += 1;
  }

  return secondHeap.getMinimum();
}
```

## Implement a Stack using a Heap

* Idea is to use the insert order as "priority"
* So have a counter that starts at `0` and increments on each `push`
  * So on `push`, the current counter value and it's associated value (whatever is being `pushed`) are added to the heap
* And then underlying heap, on `pop` will just delete whatever the latest max counter value was

## Given a big file container billions of numbers how can you find the 10 maximum numbers from that file

* When you need to find max `n` elements, the best data structure to use is priority queues / heaps
* Divide data into sets of 1000
* Make a heap from this data
* Take top 10 elements from each heap
* Put each of the top 10 elements in a heap
* Take top 10 elements from _that_ heap
* Downside is that if there are a lot of elements, the heap with all the top 10 element subsets can still take up a lot of memory
* So to avoid this, take top 10 from first 1000, and then for each subsequent 990 elements, when creating heap, add the top 10 from previous heap

## Merge `k` sorted lists with total of `n` elements

* Max heap with first elements from each list
* Iterate until heap is empty
  * Take max element in heap and add to sorted output list
  * Whatever list the last max element originated from, add the next element from that list to heap (if it isn't empty)
* The output list should be sorted
* Time complexity is `O(n x log k)`

## Maximum value in sliding window

```text
[1 3 -1] 3 5 3 6 7 - max is 3
1 [3 -1 3] 5 3 6 7 - max is 3
1 3 [-1 3 5] 3 6 7 - max is 5
```

* Use double-ended queue
* Will be inserting indices into the queue (and not values)
* Indices will be removed from front of the queue if they're outside the front of the window
* Indices will be removed from the back of the queue if they're outside the back of the window
* Indices will be removed from the back of the queue if they're less than the current index value
* At each step, whatever is at front of queue will be maximum value in window

