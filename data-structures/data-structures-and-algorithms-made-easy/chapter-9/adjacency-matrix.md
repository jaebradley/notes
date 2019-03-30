# Adjacency Matrix

* Good if graph is dense
* Requires vertices x vertices amount of storage and vertices x vertices time for matrix initialization
* If the number of edges is not proportional to vertices * vertices then runtime will be dominated by matrix initialization

## Undirected

* Each edge is represented by two bits in undirected graph
  * Edge between A and B can be represented as 1 or 0 in matrix[A][B] and also in matrix[B][A]
* When processing undirected graph, can save time by processing only half
* Also, in undirected graph, there is an "edge" from each vertex to itself
  * Matrix[A][A] is 1

## Directed

* Mark only one entry in matrix

```text
A -> B
^    |
|    v
D <- C

  A B C D
A 0 1 0 1
B 0 0 1 0
C 1 0 0 1
D 0 0 0 0
```
