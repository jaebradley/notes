# [Leetcode Pattern 1: Part 1](https://medium.com/leetcode-patterns/leetcode-pattern-1-bfs-dfs-25-of-the-problems-part-1-519450a84353)

* Post-order: left, right, root
* In-order: right, root, left
* Pre-order: root, right, left
* DFS is basically, push to stack, pop top, retrieve unvisited neighbors from top, push them to stack - continue, while stack is not empty
* Number of connected components in an undirected graph
  * Create adjacency list of edge from each node to it's neighbor
  * Starting with first node, iterate over all nodes by adding it to stack
  * While stack is not empty, pop top element, set it as visited
  * For each neighbor identified via adjacency list, if the neighbor has _not_ been visited, add it to stack
  * Each time the while loop is empty and the containing for loop that's iterating over all nodes is re-entered, if the current node at that point in time hasn't been visited, increment the connected component count
* Number of islands - similar to connected components in undirected graph
  * One a "1" is found, do DFS from that cell in all 4 directions, attempting to reach all the "1"s that are conneted to that cell, and are thus part of the same island
  * Similar to conneted components, each time dfs finishes and a new cell is considered, if that cell is found with "1" that hasn't been visited, increment the count of island
  * Optimization is that adjacency matrix is not needed if visited cells are overridden - their "1" is switched to "*" or something

```bash
    1
   / \
  2   3
 / \   \
4   5   6

# Stack evolution
[1]
[2, 3]
[4, 5, 3]
[6]

# Queue evolution
[1]
[2, 3]
[4, 5, 6]
```

* In the stack / DFS case, `3` needs to stay in stack as long as any of `2`'s sub-trees are not done
* In the queue / BFS case, `3` needs to be evaluated before any of `2`'s subtrees