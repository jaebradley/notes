# [Errors in Database Systems, Eventual Consistency, and the CAP Theorem](https://cacm.acm.org/blogs/blog-cacm/83396-errors-in-database-systems-eventual-consistency-and-the-cap-theorem/fulltext)

* Partition-tolerance: if there is a network failure that splits processing nodes into two groups that cannot talk to each other, then the goal would be to allow processing to continue in both subgroups
* Assume a typical hardware model of a collection of local processing and storage nodes assembled into a cluster using LAN networking
  * Clusters are wired together using WAN networking
* A partition in a WAN network will most likely separate a small portion of the network from the majority
  * majority can continue processing while the small portion must block
  * Doesn't make sense to give up consistency all the time in exchange for availability of a small subset of the nodes in a fairly rare scenario
