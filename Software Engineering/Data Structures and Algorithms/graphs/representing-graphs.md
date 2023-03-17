# [From Theory to Practice: Representing Graphs](https://medium.com/basecs/from-theory-to-practice-representing-graphs-cfd782c5be38)

* Graph is a data structure with two parts
  * Finite set of vertices / nodes
  * Finite set of edges which are references / links/ pointers from one vertex to another
* Graphs with a high ratio of edges to nodes are called "dense" graphs
* Graphs with a low ratio of edges to nodes are called "sparse" graphs
* Graphs with edges that have direction are called "directed" graphs
* Graphs with edges that don't have directed are called "undirected" graphs

![graph-representation](https://cdn-images-1.medium.com/max/800/1*J9U-CK1N6X7WBAnz_m04SQ.jpeg)

## Edge list

* Represent all edges in graph as an array of arrays
* For graph like `1 <-> 2 <-> 3 <-> 1` the edge list would look like

```text
[
  [1, 2],
  [2, 3],
  [3, 1],
]
```

* Only way to find if vertex `1` is connected to vertex `2` is to iterate over array and see if `[1, 2]` or `[2, 1]` exists
* Expensive to do this for larger graphs - `O(E)` time, where `E` represents the number of edges in the graph.
* Requires `O(E)` space

## Adjacency matrix

* Matrix that represents which vertices/nodes in graph have edges between them
* Matrix serves as a lookup table where value of `1` represents that an edge exists and value of `0` represents that an edge does not
* Symmetric adjacency matrices represent undirected graphs

![adjacency-matrix](https://cdn-images-1.medium.com/max/600/1*bllOr7NiKf4YbNtqg1cEqA.jpeg)

* To check if vertex `3` is connected to vertex `1`, would check column `3` and row `1` in matrix
* Can also represent adjacency matrix as an array of arrays
* Most operations on an adjacency matrix can be done in constant time

```text
[
  [0, 1, 1],
  [1, 0, 1],
  [1, 1, 0],
]
```

* For sparse graphs (i.e. graphs with less edges vs nodes), the adjacency matrix would mostly be comprised of `0`s.
* They will always require `O(V^2)` space, which can lead to a lot of "wasted" space

## Adjacency list

* An array of linked lists that serve as a representation of a graph where the array index represents a specific vertex
* The linked list stores the neighboring vertices
* Useful for graph traversal problems, because don't need to build the graph, just need to know where we can travel (aka who the neighbors of a node are)

![adjacency-list](https://cdn-images-1.medium.com/max/800/1*XPH-Z7fBfBT1mEcN03FOJA.jpeg)

* How to check edge between two nodes?
  * Retrieve vertex's possible neighbors takes `O(1)` since just have to get lookup index and use index to retrieve values from array
  * Iterate through neighbors and see if other vertex that will make up edge exists
  * Worst-case this will take `O(d)` time where `d` is degree of matching vertex
    * `degree` of a vertex is the number of edges that it has, which is also the number of neighboring nodes
* Maximum possible value for `degree` is number of total vertices minus `1` (every single node in graph except itself)
* Adjacency list will take up `V` space, since every vertex is going to need its own index in array
* For undirected graphs, each edge is going to get represented twice in list

![undirected-adjacency-list](https://cdn-images-1.medium.com/max/600/1*OuGzFyh22HxjzqLUyNsHDA.jpeg)

* For directed graphs, each edge is going to get represented once in list

![directed-adjacency-list](https://cdn-images-1.medium.com/max/600/1*wBqdISORWCjHLqf9qZ_N-A.jpeg)
