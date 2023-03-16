# [How to Use Consistent Hashing in a System Design Interview?](https://medium.com/interviewnoodle/how-to-use-consistent-hashing-in-a-system-design-interview-b738be3a1ae3)

* Consistent hashing maps data to physical nodes and ensures that only a small set of keys move when servers are added or removed
* Ring of data (let's say, values from 1-100)
  * If there are four servers, Server 1 would be responsible for values 1-25, Server 2 would be responsible for values 26-50, etc.
* The start of the range is called a "token"
  * End of the range is next "token" - 1
* Hash the key, and depending on where the hash lies on the range of values, will also determine where the data is stored
* When a node is added or removed from the ring only the next node is affected
  * If a node is removed, the next node becomes responsible for all the keys stored on the outgoing node

## Virtual Nodes

* Instead of assigning a single token to a physical node, the "hash range" (i.e. 1-25 from before) is divided into multple smaller ranges and each physical node is assigned several of these smaller ranges
  * Each of these smaller ranges is considered a virtual node / token
  * Physical node is now responsible for many tokens / virtual nodes
* Virtual nodes should be randomly distributed and non-contiguous so that no two neighboring virtual nodes are assigned to the same physical node
  * You can imagine a circle being divided into 16 equal parts
  * If there there four servers, then Section 1 might go to Server 1, and Section 2 might go to Server 2, etc. so that each Section / token / virtual node is assigned a different Server than its neighbors
  * You can also imagine that Section 1 and Section 5 are replicated for fault tolerance - would want to split these Sections across different Servers
* With virtual nodes, can assign large numbers of sub-ranges to more powerful machines and lower number of sub-ranges to less powerful machines
* Smaller ranges for every physical node, decreasing probability of hotspots

## Data Replication

* Each key is assigned a coordinator node, which first stores the data locally and then replicates it to N-1 (where N is the replication factor) clockwise successor nodes on the ring
* So if there are 6 servers distributed in the hash ring
  * If some Key falls between Server 6 and Server 1 with a replication factor of 3, the Key / Value would be stored on Server 1 and replicated on the next 2 Servers (Server 2 and Server 3)
