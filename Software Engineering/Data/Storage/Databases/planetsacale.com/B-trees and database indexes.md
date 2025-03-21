# [B-trees and database indexes](https://planetscale.com/blog/btrees-and-database-indexes)

* Databases store pairs of data with a “key” and a “value”
* Each node in the tree stores N key/value pairs
* N is > 1 and <= K
* Each node that is not the leaf or the root stores at least N/2 key/value pairs
* Each node has N + 1 children
* All leaves are on the same level
* Within each node, the values are kept in order
* Any child to the left of a key must only contain other keys that are less than it
* Any child to the right of a key must only contain keys greater than it
* Search algorithm looks something like
  * Check if the ndoe contains the key you are looking for
  * If not, find the location in the node where your key would get inserted into (i.e. which child node it would be contained within)
  * Move to the child node and introspect that node in a similar way
* B-trees work well when you have a large quantity of data that needs to be persisted in long-term storage (disk)
* Each node uses a fixed number of bytes
* The number of bytes can be tailored to play nicely with disk blocks such that each node can be sized to match the size of a disk block
* Key + values + child pointers are all 8 bits
* Disk block and B-tree nodes are 16kb in size
* Each node could store 682 key/values pairs and 683 child pointers per node
* A 3-level tree could store over 300 million key/value pairs

## B+tree
* Key/value pairs are only stored at the leaf nodes
* Non-leaf nodes only store keys and associated child pointers
* B+trees in MySQL indices have additional rules:
  * Non-leaf nodes store N child pointers instead of N+1
  * All nodes contain `next` and `previous` pointers, allowing each level of the tree to act as a doubly-linked list
  * In other words, each node in the same level of a tree can go to the next/previous node in the level of the tree
  * Inner nodes don’t store values, so more keys can be stored, keeping the tree shallower
  * All values are stored at the same level, traversed in-order via the leaf-level linked list

## B+ Trees in MySQL
* Whenever MySQL needs to access a piece of data it loads the entire associated page/B+ tree node from disk (16kb by default) even if that page contains other keys or values it does not need
* An additional persisted B+tree is cosntructed for each secondary index
* The “key” for this index are the colums that the index is built for
* The “values” are the primar keys for the associated row
* A search is performed on the secondary index B+tree
* Primary keys for matching results are collected
* Primary keys are used to search on the main table B+tree to find the actual row data

### Insertions
* Using a UUID as a primary key means that the specific node where data might be inserted is unpredictable ahead of time
* This means that any intermediate/internal nodes are not known ahead of time
* Over the course of many inserts, many different nodes/pages will be visited
* This reading and writing will lead to poor performance
* Using an auto-incrementing primary key, in contrast always inserts along the right-most path of the tree
* Similarly, leaves are only added on the right side of the tree
* At the leaf level, data is in sorted order based on when it was inserted
* Fewer I/O requests when inserting lots of key/value pairs

### Pages and InnoDB
* Buffer pool is an in-memory cache of InnoDB pages
* When MySQL needs to read a page, it first checks to see if it’s already in the buffer pol
* If so, it reads the page from the buffer pool, skipping the need to execute a disk I/O operation
* If the page doesn’t exist in the buffer pool, it requests the page from disk, adds it to the buffer pool, and continues with query execution
