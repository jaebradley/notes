# [How to implement Dijkstra's Algorithm in JavaScript](https://medium.com/hackernoon/how-to-implement-dijkstras-algorithm-in-javascript-abdfd1702d04)

* Graph is abstract data type used to model set of connections
  * Made up of nodes and connections between nodes (edges)
  * Graphs can be directed so that edges are one-way or undirected where connections run both ways
  * Edges can be weighted which can often represent the cost of getting from a given node to another node
* Algorithm
  * Find the cheapest node (that hasn't been visited)
  * Update cost of neighbors of node (if cost is cheaper)
  * Do previous two steps for all nodes
  * Return lowest cost to reach final node, and path
* Keep a map of nodes to costs
  * Initial costs will be infinite for all nodes
* Keep a map of parents of each node
  * Initial parents are `null`
* Need to keep track of processed nodes
  * Processed means that already calculated cost to reach each of node's children
  * Can use array / set

```javascript
let node = lowestCostNode(costs, processed);
while (node) {
  // Get cost of current node
  let cost = costs[node];
  // Get children of node
  let children = graph[node];
  for (let n in children) {
    // Update costs for children if the costs don't exist
    let newCost = cost + children[n];
    if (!costs[n]) {
      costs[n] = newCost;
      parents[n] = node;
    }
    // Update costs for children if the costs do exist and the new cost is less than the current stored cost
    if (costs[n] > newCost) {
      costs[n] = newCost;
      parents[n] = node;
    }
  }
  processed.push(node);
  node = lowestCostNode(costs, processed);
}
```
