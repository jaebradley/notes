# [Google Interview Problems: Ratio Finder](https://medium.com/@alexgolec/google-interview-problems-ratio-finder-d7aa8bf201e3)

* Given a list of conversion rates (origin unit, destination unit, and conversation rate) design an algorithm to take two arbitrary units and return a conversion rate
* Treat units as a graph
  * Each unit is a node in a graph
  * If edge exists from node `A` to node `B`, `A` can be converted to `B`
* Start at origin unit and search until find destination unit
  * If no destination is found, there is no path
* DFS
  * Starts at node
  * Iterates over neighbors
  * Recursively calls search on their neighbors
  * Keeps track of visited node using a Set
  * Keeps calculating conversion rate between origin node and intermediary nodes so that once at destination node, already built up conversion rate
  * Weaknesses
    * Could lead to long paths (thousands of hops could lead to crash)
    * Leads to floating point inaccuracies (want to perform as few floating point multiplications as possible)
* BFS
  * Start at node
  * Visit nodes in each generation
  * Use queue to insert each neighbor's neighbors
  * Guarantees "shortest-path" so minimizes floating point multiplication errors
  * Runtime is `O(Nodes + Edges)`
* Optimizing for performance
  * Caching all edges from node A to node B isn't possible as takes up a lot of space
    * Number of edges possible in a graph grows with half the square of the number of nodes
  * Instead of throwing away intermediate conversions when iterating through graph, keep them
    * To calculate rate of A to B, calculate rate from X to B and divide by rate X to A
    * Can store this conversion data in a Map where the key is a given unit and the values are
      1. The other unit (X) that A is being converted to
      1. The conversion rate between other unit and A
    * Whenever a unit isn't in the Map, it triggers a BFS

