# [Learning Tarjan's algorithm](https://dwf.dev/blog/2024/09/25/2024/learning-tarjans-algorithm/)
* Strongly-connected components are self-contained cycles within a directed graph where every vertex in a given cycle can reach every other vertex in the same cycle
* These cycles can be "collapsed" into different vertices that represent the entire component

<img width="546" height="191" alt="image" src="https://github.com/user-attachments/assets/609154e0-a41f-46da-ab48-c794fd6185b5" />

* Edges between super-vertices: `ABF` -> `GH`, `CDI` -> `GH` and `CDI` -> `ABF`, and `E` -> `E` and `E` -> `CDI`
* Tarjan's algorithm ultimately returns the reverse of the topological ordering, a reverse topological sort of a graph's super-vertices / strongly-connected components
  * In the previous example, the order would be `E` -> `CDI` -> `ABF` -> `GH`
 
## Low-link values
* The low-link value is the smallest (lowest) node that is reachable from a given node via DFS
* Each node is given a number that maps to its order when visiting via DFS
* Low-link values are dependent on the order in which nodes are explored during DFS
* Tarjan's algorithm maintains a set, often implemented as a stack, of valid nodes from which to update low-link values
* Nodes are pushed to the stack of valid nodes as they're explored for the first time
* Nodes are popped from the stack each time a complete strongly-connected component is found
* If `u` and `v` are nodes in a graph and we're currently exploring `u`, then low-link update condition occurs when
  * There's a path of edges from `u` to `v`
  * Node `v` is on the stack
  * Then update `u`'s low-link value to `v`'s low-link value

## High-level overview of Tarjan's algorithm
* Mark the id of each node as unvisited
* Start DFS from any node
  * When visiting a node, assign it an id and a low-link value equivalent to that id
  * Increment the id for the subsequent node's id
  * Mark current node as visited and add them to the seen stack
* On DFS "callback" when recursive calls to DFS finish and backtracking begins
  * If the node that we are backtracking from is on the stack, take the minimum of the current node's low-link value with the low-link value of the node that is being backtracked from
  * This allows low-link values to propagate throughout cycles
  * After visiting all neighbors, if the current node started a connected component then pop nodes off the stack until the current node is reached
  * The node that started a connected component has an id that is equal to its low-link value

```python
# T: O(V + E); S: O(V)
def tarjan(graph):
    n = len(graph)          # graph assumed to be adjacency list of index arrays
    node_id = 0             # unique node_id assigned to each node
    ids = [-1] * n          # id of each node
    low_link = [0] * n      # store lowest id node reachable from each node
    on_stack = [False] * n  # boolean array to check if a node is on the stack
    stack = []              # stack to keep track of nodes in current SCC
    sccs = []               # list to store all SCCs

    def dfs(node):
        nonlocal node_id
        low_link[node] = node_id    # initialize low_link value of node to be its own node_id
        ids[node] = node_id         # assign smallest unused node_id to node
        node_id += 1                # increment node_id value to ensure later nodes have higher node_id values
        stack.append(node)          # push node onto the stack
        on_stack[node] = True       # mark node as being on the stack

        # explore all outgoing edges from the node
        for nbr in graph[node]:
            if ids[nbr] == -1:      # visit nbr if it has not yet been visited
                dfs(nbr)
            if on_stack[nbr]:       # nbr is on stack, thus part of current SCC (minimize low-link on callback)
                low_link[node] = min(low_link[node], low_link[nbr])

        # if node is a root node of an SCC, pop from stack and generate SCC
        if ids[node] == low_link[node]:
            scc = []
            while stack:
                scc_nbr = stack.pop()
                on_stack[scc_nbr] = False
                low_link[scc_nbr] = ids[node]
                scc.append(scc_nbr)
                if scc_nbr == node:
                    break
                
            sccs.append(scc)  # add SCC to overall list of SCCs

    # initialize top-level DFS traversals
    for v in range(n):
        if ids[v] == -1:
            dfs(v)
```
