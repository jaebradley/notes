# [How To Merge K-Sorted Arrays](https://medium.com/outco/how-to-merge-k-sorted-arrays-c35d87aa298e)

* For two arrays, you can use two pointers while iterating through each array to find next largest element
* However, this doesn't scale with the number of arrays (with three arrays, you need three pointers - with four arrays, you'll need four pointers, etc.)
* Can solve this problem by concatenating all arrays together and applying efficient sorting function (but generally, this is considered "naive" approach)
* Key to problem is knowing the current smallest value for each array and know how to get next smallest, efficiently

## Min heaps to rescue

* Parent nodes are smaller than children
* Nodes are inserted in breadth-first order
* No relationship between siblings
* Strategy is to use min heap to keep track of smallest number from each array
* The size of this min heap will be the number or arrays `K`
* Iterate through all the values across every array until min heap is empty / all values have been added to result array
* At each node, you'll need to know which array and which position in that index a value came from
* When the parent node is used, add back the next array element from that parent node's array
* Time complexity is `NK * log K`
  * `NK` is the total number of elements across `K` arrays where `N` is number of elements in the array
  * `log K` represents the maximum number of bubble-down operations per element
