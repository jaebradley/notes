# Definitions

* When an edge connects two vertices, the vertices are said to be adjacent to each other
  * The edge is said to be _incident_ on both vertices
* A graph with no cycles is called a tree - an acyclic connected graph
* A graph is connected if there is a path from every vertex to every other vertex
* A graph is unconnected (like in example below) when it consists of set of unconnected components

```text
A - B
  \
   C

D-E
```

* Two edges are parallel if they connect the same pair of vertices
* The _degree_ of a vertex is the number of edges _incident_ on it
* Graphs with relatively few edges (edges < |vertices| log |vertices|) are called _sparse_
* Graphs with relatively few of the possible edges missing are called _dense_
