# [Topological Sort](https://www.interviewcake.com/concept/java/topological-sort)
* Sorts a directed graph and returns array of nodes where each node appears before all nodes that the node points to
* Graphs can have more than one valid topological ordering
* Cyclic graphs don't have a valid topological ordering

## The Algorithm
* The first node in the topological ordering is a node that does *not* have any incoming directed edges
* In other words, this node has an indegree of zero
* When this zero indegree node is added to the total topological ordering, identify the node's *outgoing* edges and decrement the indegrees for the destination nodes of the target node
* After the decrementing, find the next zero indegree node and repeat this process
* If there are nodes with non-zero indegrees while there are also no nodes with zero indegrees, then the graph has a cycle and no topological ordering exists
* Time complexity: number of edges + number of nodes
* Space complexity: number of nodes
