# [Going Broad In A Graph: BFS Traversal](https://medium.com/basecs/going-broad-in-a-graph-bfs-traversal-959bd1a09255)

* Searching and traversing through a graph data structure involves visiting each vertex/node in a graph
  * The order in which vertices are visited is how they can classify graph traversals
* Depth-first
  * Traverses deep into a structure by visiting children nodes before visiting sibling/neighbor nodes
  * Uses a `Stack` data structure
* Breadth-first
  * Traverses broad into a structure by visiting sibling/neighbor nodes before children nodes
  * Uses a `Queue` data structure

## BFS

* Add node/vertex from graph to queue of nodes to be visited
* Visit the first node in queue and mark it as visited (either by changing property on node or maybe a set)
* If node has neighbors, check to see if they have been visited
* Add neighboring nodes that still need to be visited to the queue
* Remove visited node from queue

![example](https://cdn-images-1.medium.com/max/1200/1*uwLddOZZksio58lCixU-Dw.jpeg)
![level-1](https://cdn-images-1.medium.com/max/1600/1*WCK3Hsd5k2ysYDKbH6gMZg.jpeg)
![level-2](https://cdn-images-1.medium.com/max/1200/1*kS-IL-XQCavRjxgvu8jnng.jpeg)
![level-3](https://cdn-images-1.medium.com/max/1600/1*NLG1tGpThkWr8A84nzU88A.jpeg)
![emptying-queue](https://cdn-images-1.medium.com/max/1200/1*N651ddMq3TlVayiROpeYZQ.jpeg)

* Each time we add neighboring nodes to the queue, that indicates the "level" that those nodes are at
* For example, in the above examples, node `h` is a level 3 node and has 3 edges to return back to main parent node (`b`)

![shortest-path](https://cdn-images-1.medium.com/max/1200/1*hUVFsOzehynVr1JNWWhzaQ.jpeg)

* The pointers that lead back to the "parent" node can be rearranged to form a tree

```text
      b
     / \
    f   a
   / \   \
  g   c   e
 /     \
h       d
```

* Backtracking from a node and follow one of its parents until reach the main parent node will have found one of the shortest paths to node, which is equivalent to level of that node

## BFS Using Adjacency List

![adjacency-list](https://cdn-images-1.medium.com/max/1600/1*aOCvuDauXWO_8UQnXjd0EQ.jpeg)

* Adjacency list
  * Array, where index references node in graph
  * Each index references linked list of neighboring vertices
  * Linked list contains references to the index in adjacency list of neighboring nodes
* When doing BFS, add "visited array", where index references index in adjacency list

### BFS Algorithm Using Adjacency List

* Choose arbitrary starting node - mark it as visited by changing it's state in visited array to `TRUE`
* Enqueue node
* When dequeue node, iterate through associated linked list
* If any neighbor in linked list has not been visited (i.e. visited array index value for node is `FALSE`) then mark it as visited and enqueue
* Continue doing this until queue is empty

## Time Complexity

* Only iterate through vertex's adjacency list once
  * For each vertex, we look at edges / neighboring nodes once
* For undirected graph, edge would appear twice, in two different adjacency list indices
* Time complexity is `visiting n nodes + number of edges in adjacency list to be visited`
* Which boils down to `O(V + E)`
