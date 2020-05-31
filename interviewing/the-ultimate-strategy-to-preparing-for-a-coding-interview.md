# [The Ultimate Strategy To Preparing For A Coding Interview](https://medium.com/better-programming/the-ultimate-strategy-to-preparing-for-the-coding-interview-ee9f7eb439f3)

* Sample Two-Pointer Problem: Pair with a target sum
  * Given an array of sorted numbers and a target sum, find a pair in the array whose sum is equal to the given target
  * Given the array is sorted, a brute-force solution is to iterate through the array taking each number and then searching for the second number - this is `O(N Log N)`
  * Start with one pointer at beginning of the array and another at end
  * If sum of two numbers is greater than the target sum, then need smaller value, so decrement end pointer by one index
  * If sum of two numbers is smaller than target sum, then need greater value, so increment start pointer by one index
* Sample Heap Problem: K Closest Points To Origin
  * Given an array of 2D points, find K closest points to origin
  * Create a max heap that compares the Euclidean distance from the origin / Pythagorean theorem
  * Push first `k` points to heap
  * Then for remaining points, if the Euclidean distance for the point is less than top point in max heap, remove the top point in max heap and add the current point
  * After iterating through all points, heap should contain all points closest to origin
  * If dealing with top/maximum/minimum/closest `k` elements among `n` elements, need to use heap
* Sample Problem: Binary Tree Path Sum
  * Given a binary tree and a number `S`, find if the tree has a path from root-to-leaf such that the sum of all the node values of that path equals `S`
  * Since it's `root-to-leaf` problem, use DFS
  * Sub-problem at each node is to find a route from that node that sums to a given value, where that value is the value at the parent node minus the parent node's value
  * In more concrete terms, if the target sum at the parent node was `20`, and the parent node's value was `3`, then the target sum for the left or right children is `17`
  * Check left child subtree then right child subtree recursively
  * If current node is a leaf node and it's value is equal to the target sum then a path from root to leaf is found so return `true`
  * If current node is a leaf node but it's value is not equal to the target sum then return `false`