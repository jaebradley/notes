# Adjacency List

* The vertices connected to a vertex `v` are part of a linked list on vertex `v`

```text
A -> B
^    |
|    v
D <- C

A -> B
B -> C
C -> D
D -> A
```

* Order of edges is important because the same graph can be represented in different ways in adjacency list
* The order of edges in adjacency list affect the order in which edges are processed
