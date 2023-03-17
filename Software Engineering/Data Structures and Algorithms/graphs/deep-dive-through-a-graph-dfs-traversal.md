# [Deep Dive Through A Graph: DFS Traversal](https://medium.com/basecs/deep-dive-through-a-graph-dfs-traversal-8177df5d0f13)

* DFS allows us to determine whether two nodes have a path
  * BFS is good for finding "shortest path"
* DFS algorithm is much like solving a maze - follow a path until you can't follow it anymore, then backtrack and retrace steps until you find another possible path to follow

## DFS, In Action

* Arbitrarily choose any node to start with
* For every node that we visit, we'll keep track of where we came from and use that to
  * backtrack when we need to
  * easy way to keep track of the path that we're constructing through graph
* Simple way of keeping track of which node we're currently searching is by employing a stack data structure
* Recursively visit every single node that is reachable from starting node (node `a` in example).

![node-c](https://cdn-images-1.medium.com/max/1600/1*ku4di0nVGwOpRMugZImJ3g.jpeg)

* Do this until we reach node `e` (aka the end), so backtrack by popping `e` off the stack and check `d` for any more children
  * Mark `e` as "visited" as well

* DFS requires `O(V + E)` runtime

## DFS, in real-life

![adjacency-list](https://cdn-images-1.medium.com/max/1200/1*kYMRBN0VD1YuYlOkYJ2JAQ.jpeg)

* Adjacency list + "visited" array + stack of nodes
* When first visit "parent" node `a`
  * Mark "visited" array at index of node in adjacency list (index `0`)
  * Push node `a` to stack
* Take a look at first item in node `a`'s linked list (in adjacency list) - node `c` at index `2`
  * Do this instead of iterating through rest of items in linked list
  * Mark node at index `2` as visited
  * Add node `c` to stack
  * Check **its** first neighbor in adjacency linked list
  * Finse, repeat
  * When get to end of linked list, pop off stack and go to element at top of stack, and go to next element that hasn't been visited

## DFS vs. BFS

* Both can be implemented to use an adjacency list representation of graph
* Both result in same runtime
* DFS is better for determining if path exists between two nodes and doesn't necessarily require a lot of memory
  * Not great at finding shortest path - may inadvertently find longest path
* BFS is great at finding shortest path, but requires storing entire graph as search through level by level
