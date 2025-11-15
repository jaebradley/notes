# [A Visual Introduction to Fenwick Tree](https://medium.com/carpanese/a-visual-introduction-to-fenwick-tree-89b82cac5b3c)
* Given an array, there are two operations
  * Find the sum of all elements in an interval
  * Change the value at index `x`
* Represent the data in a binary tree such that the nodes in the binary tree contain the answers for segments of the array
* The root node has the sum of the entire array
  * The left child of the root node has the sum of the first half of the array
  * The right child of the root node has the sum of the last half of the array

<img width="882" height="396" alt="image" src="https://github.com/user-attachments/assets/f954a688-c914-4b45-ba50-736ab8e16add" />

<img width="971" height="438" alt="image" src="https://github.com/user-attachments/assets/fcff826a-47b1-43ab-8a1e-87b6adb0ef19" />

* The values in the even indices can be inferred from the other values

<img width="944" height="452" alt="image" src="https://github.com/user-attachments/assets/760a6fc4-539b-43a8-b887-da532e7c7003" />

* The idea of summing up to index 13 is to sum block 13, then block 12, and then block 8
  * Block 8 represents the sum of all the values on the left half of the array
  * Block 12 represents the sum of all the values up to the 13th index
  * 13 = 1101 in binary

<img width="954" height="245" alt="image" src="https://github.com/user-attachments/assets/3d5e4a1c-e6f2-4ee9-aa7b-110bc64b94a9" />

```cpp

#define lsb(n) (n & (-n))

int op(int a, int b) {
    return a + b;
}

int query(int n) {
    int res = 0;

    while (n > 0) {
        # keep summing the result
        res = op(res, fenwick[n])
        # subtract by previous least significant bit;
        n -= lsb(n);
    }

    return res;
}
```

* When updating a node value, we would then need to update all node values "above" the node

<img width="881" height="709" alt="image" src="https://github.com/user-attachments/assets/20cdc452-4b98-4a3b-bff6-ba72beac2382" />

* All nodes have the same suffix at the same level of the tree
* Fenwick Tree must be 1-indexed
  * Zero has no significant bit which means that the size of its respective block is zero
  * A zero-sized block does not make any sense
* All numbers that end with a certain suffix must appear in that level
* Prefix indicates the order of that index in a given level
* Let A and B be two nodes whose indices start with the same digits in their binary representation
  * If the length of A's prefix is less than the length of B's prefix, then A is an ancestor of B
* To update a value of the array, need to move from a given node B to node A immediately above it
  * Although not intuitive, node A is the sum of node B's index + node B index's least significant bit
