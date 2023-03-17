# Commit Graphs

* Commit graphs are directed acyclic graphs
  * Edges within the graph are directed from one node to another
  * Starting at any node in the graph, there is no path along the directed edges that leads back to the starting node
* Each node in commit graph is a single commit
* All edges are directed from one descendant node to a parent node
  * Remember that newer commits point back to older commits
  * This implies that the node that doesn't have a parent is the initial commit
* A parent commit that has more than one descendant implies that it is the origin of a branch
* A descendant commit that has more than one parent implies a merge
