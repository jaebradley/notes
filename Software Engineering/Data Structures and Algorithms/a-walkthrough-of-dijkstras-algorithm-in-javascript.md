# [A Walkthrough of Dijstra's Algorithm (in JavaScript)](https://medium.com/@adriennetjohnson/a-walkthrough-of-dijkstras-algorithm-in-javascript-e94b74192026)

* Location map is a weighted graph
  * Locations are nodes of graph
  * Paths between them are edges
  * Time to get from one node to another node is the weight of the edge

```javascript
class Graph {
  constructor() {
    this.nodes = [];
    this.adjacencyList = {};
  }

  addNode(node) {
    this.nodes.push(node);
    this.adjacencyList[node] = [];
  }

  addEdge(node1, node2, weight) {
    this.adjacencyList[node1].push({ node: node2, weight });
    this.adjacencyList[node2].push({ node: node1, weight });
  }
}
```

* Adjacency list for a given node holds all neighboring nodes
  * Another way to think about it is the endpoint of every edge extending from node

## The algorithm

* Move to a node that hasn't been visited, choosing fastest node to get to first (i.e. the one with the smallest `weight` value)
* At given node, check all neighboring nodes
  * Add neighbor's `weight` to time it took to get to current node
* If calculated time is faster than previously known shortest time to get to any neighboring nodes
  * If time is faster, update previously known shortest time to get to that node
    * This is done by updating the previous node for a given node (so if going from `A -> B -> C` is faster than `A -> C` the previous node for `C` would be `B`)
  * Add node to list of nodes to visit next
  * List of nodes to visit next are arranged in order of shortest calculated time to reach using priority queue

## Building Priority Queue of Nodes to Visit Next

* Priority is queue that orders values based on `weight`

```javascript
class PriorityQueue {
  constructor() {
    this.collection = [];
  }

  enqueue(node) {
    if (this.isEmpty()) {
      this.collection.push(node);
    } else {
      let added = false;
      for (let i = 1; i <= this.collection.length; i++) {
        if (node.weight < this.collection[i - 1].weight) {
          this.collection.splice(i - 1, 0, node);
          added = true;
          break;
        }
      }

      if (!added) {
        this.collection.push(node);
      }
    }
  }

  dequeue() {
    return this.collection.shift();
  }

  isEmpty() {
    return this.collection.length === 0;
  }
}
```

## Walkthrough Implementation

* Know that shortest time to get to start node is `0`
* Start time for all other nodes is `Infinity`
* Access first element in queue and check neighbors, using adjacency list
* For each neighbor, add weight
* If calculated time is less than time on file for neighbor
  * Update time for node as calculated time
  * Update "backtrace" map (as author calls it) to point previous node with shortest path to current node
    * Could also use a previous node reference or property
  * Then add neighbor to priority queue (again, ordering by their weight)
* Can use "backtrace" map to step back from end destination to start destination where each node in "backtrace" should be part of shortest weight path
