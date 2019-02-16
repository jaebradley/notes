# [Spinning Around in Circles with Directed Acyclic Graphs](https://medium.com/basecs/spinning-around-in-cycles-with-directed-acyclic-graphs-a233496d4688)

* DFS can be are useful in the context of graphs
  * identification of cycles
  * identification of different types of edges
  * the order in which vertices are visited
* Edges can be directed (from one node to another) or undirected (bidirectional flow between the two nodes)
* 4 edge types
  * Tree edge
    * edge when moving from visited node to unvisited node
    * edge encountered when building a tree that represents the path in which the graph was traversed
  * Non-tree edges
    * Forward edge
      * connects a node to a child node
      * example is when a node has two directed children
        * whatever edge is unvisited when traversing the graph is the forward edge
      * can only occur in directed graph
        * this is because in the previous example, the unvisited edge would eventually end up getting visited
    * Backward edge
      * connects a node to an ancestor node
      * ancestor could be parent, grandparent, itself
      * if a backward edge exists, the graph has a cycle
    * Cross edge
      * connects one subtree of a graph with another subtree of the graph
      * can only occur in directed graph
      * connects nodes that don't necessarily share an ancestor in tree path
* Graph is interconnected forest of smaller subtrees
* Directed graph can have all four edge types
* Undirected graph can only have tree edge or backward edge
* A self-loop (i.e. a node referencing itself) can only occur in a directed graph, since the self-loop edge is a form of a directed edge
* DFS traversal of graph adds nodes to stack
  * If any added nodes reference a node already in stack then there's a cycle
* Directed Acyclic Graphs
  * a directed graph that does not have any cycles
  * starting at any node in the graph will not allow loop back to starting node
  * used for scheduling, especially when processing order matters
  * used for dependency graphs and dependency resolution
* Topological sort
  * If there are two vertices connected by a directed edge, topological sort will order the vertices in their directed order
  * Implies that can only visit a vertex if all previous vertices have been visited
  * If there's a vertex A that has directed child vertices B and C and both these vertices point to a child node D, D can only be visited if A, B, and C were visited
  * Can only be run on DAGs since unclear where sort would start (consider simplest case of a self-loop)
  * Start search on parent node (no edge leading to it)
  * Use DFS and then assign some order to last node in a search path
    * then when back tracking up search path keep track of the order of pervious node
    * then when going down any unvisited path increment parent's order value
