# [Algorithms Behind Modern Storage Systems](https://queue.acm.org/detail.cfm?id=3220266)

## B-trees

* B-trees are read-optimized indexing data structure (`MySQL`, `Postgres`, `HFS+`)
* Keeping binary tree balanced keeps tree depth at minimum
* Rebalancing binary tree can be done via rotation
  * Take parent node of longer subtree and move it below its child

```bash
    5      3
   /      / \
  3  =>  2   5
 /
2
```

* Binary trees are most useful as in-memory data structures
  * This is due to balancing and low fanout (2 pointers per node) which don't work well on disk
* B-trees allow for storing more than two pointers per node
* B-trees are
  * Sorted: allowing sequential scans which simplifies lookups
  * Self-balancing: No need to balance tree during insertion and deletion - when a B-tree node is full, it is split into two and when it's sparse, nodes are merged
    * Also implies that nodes are equally distant from root
  * Guarantees logarithmic lookup time: B-trees are useful for database indices, where lookup times are important
  * Mutable: inserts, updates, and deletes are performed on disk in-place

### Anatomy of the B-tree

* B-trees have three node types: `root`, `internal`, and `leaf`
  * `root` node is the top-level node that has no parents
  * `internal` nodes have a parent and children
  * `leaf` nodes contain the data and only have parents
* Branching factor: number of pointers to child nodes
* Occupancy: number of pointers to child items the node is currently holding, out of maximum available
* Height: number of B-tree levels, signifying how many pointers have to be followed during lookup
* Node ordering
  * Non-leaf nodes in the B-tree have entries which point to a subtree where all the entries in that subtree are within some range of values
  * These subtrees are ordered in such a way that
    * the preceding subtree has values that are less than the current subtree
    * the subsequent subtree has values that are greater than the current subtree
  * This ordering is implemented using the first and last pointers
* B-trees use binary search, which will have logarithmic complexity, since nodes are ordered
* Insertion
  * Find the target leaf using binary search
  * If the leaf doesn't have space
    * create a new leaf and move half of the elements to it
    * add pointer to new leaf from parent
    * if parent doesn't have space, then parent needs to be split
  * When root overflows, contents are split between newly allocated nodes
  * Implies tree and height grows by splitting root node

## LSM-tree

![lsm-tree](http://deliveryimages.acm.org/10.1145/3230000/3220266/petrov5.png)

* Log-structured merge-tree is an immutable disk-resident write-optimized data structure
* Most useful in systems where writes are more frequent than lookups that retrieve records
* To allow sequential writes, LSM-trees batch writes and updates in memory-resident tables which then are "flushed" after they reach a certain size
  * This "flush" operation writes this data to disk
* Reads are served by hitting both disk and the memory-resident tables where data is merged after a reconciliation process

### Sorted String Tables (SST)

![sstable](http://deliveryimages.acm.org/10.1145/3230000/3220266/petrov6.png)

* SSTs are split into two parts - data and index blocks
  * data block is sequentially written unique key/value pairs, ordered by key
  * index block contains keys mapped to pointer that points to where the actual record is located
  * index block is often implemented using B-tree or hash table (for quick searches)
* SSTs have nice properties
  * Finding values by a key can be done quickly by looking up the primary index
  * Scans / iterating over all key/value pairs in a specified key range) can be done efficiently by reading key/value pairs sequentially from the data block

#### Lookups

* Retrieving data requires searching all SSTables on disk, checking the memory-resident tables, and merging their contents before returning the result
* Deletes in an LSM-tree insert placeholder ("tombstones") specifying which key was marked for deletion
* Updates are just records with a later timestamp
* When data is read, records that have deletes associated with them are skipped
* With updates, given the two records with the same key, only the one with the later timestamp is returned

**Table 1**
[ Alex, ts: 100 ]
[ John, ts: 100 ]
[ Sid, ts: 100 ]

**Table 2**
[ Alex, ts: 200 ]
[ DELETE John, ts: 200 ]
[ Nancy, ts: 200 ]

**Merged**
[ Alex, ts: 200 ]
[ Nancy, ts: 200 ]
[ Sid, ts: 100 ]

* To reduce the number of searched SSTables, many storage systems employ a data structure known as a Bloom filter
  * Bloom filter is used to tell if the key "might be in an SSTable" or "is definitely not in an SSTable"
  * SSTables with a negative Bloom filter match are skipped during the query

#### LSM-tree maintenance

* SSTables will grow in size (data for same key located in multiple files, multiple versions of the same record, redundant records that get shadowed by deletes) and so reads will continue getting more expensive
* LSM-trees use a merge-sort based compaction process that reads complete SSTables from disk and merges them
  * Because SSTables are sorted by key and compaction works like merge-sort, compaction is very efficient
  * Records are read from several sources sequentially, and merged output can be appended to the results file immediately, also sequentially as to preserve order
  * Merge-sort can work efficiently for merging large files that don't fit in memory

## Atomicity and Reliability

* B-trees and LSM-trees batch operations in memory before making actual updates
* This means that applying operations atomically and ensuring that data is not lost in the event of a power failure (for example) are not guaranteed
* A WAL (write-ahead log) is used to store database state modifications, and then if the batch operation crashes, the log is replayed
* B-trees generally have small WALs as they serve as backups for in-flight operations - once those operations have completed, the WAL is discarded
* LSM-tree WALs persist changes that have reached the memory-resident tables but have not yet been fully flushed
  * When a memory-resident table is flushed the WAL segment holding that data can be discarded
