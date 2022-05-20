# [Bigtable overview](https://cloud.google.com/bigtable/docs/overview)

* A single value in each row is indexed - the row key

## Bigtable storage model

* Tables are sorted key/value maps
* Each value typically no larger than 10MB
* Table is composed of rows, each of which typically describes a single entity
* Table is composed of columns, which contain individual values for each row
* Each column is identified by a combination of the column family and the column qualifier
  * Column qualifier is a unique name within the column family
* Each row / column intersection can contain multiple cells
  * Each cell contains a unique timestamped version of the data for that row and column
  * Storing multiple cells in a column provides a record of how the stored data for that row and column has changed over time
  * Tables are sparse, if a column is not used in a particular row, it does not take up any space

## Bigtable architecture

* Client requests go through front-end server before they are sent to a Bigtable node
* Nodes are organized into Bigtable clusters
* Bigtable clusters belong to Bigtable instances, which are containers for the cluster
* Each node in the cluster handles a subset of the requests to the cluster
* By adding nodes to a cluster, you can increase the number of simultaneous requests that the cluster can handle, as well as the maximum throughput for the entire cluster
* If replication is enabled by adding a second cluster, different types of traffic can be sent to different clusters, and can switch to other cluster if the primary cluster becomes unavailable
* Bigtable tables are shraded into blocks of contiguous rows, called tablets to help balance the workload of queries
* Tablets are stored on Google's file system in SSTable format
* SSTable format are ordered immutable maps of keys to values where both keys and values are arbitrary byte strings
* Each tablet is associated with a specific Bigtable node
* All writes are stored in the shared log as soon as they are acknowledged by Bigtable
* Data is never stored in Bigtable nodes themselves - each node has pointers to a set of tablets stored on Colossus
* Rebalancing tablets from one node to another is fast because actual data is not copied, instead the set of pointers is updated for each node
* Recovery from the failure of a Bigtable node is very fast, because only metadata needs to be migrated to the replacement node
* When a node fails, no data is lost

## Load balancing

* Each Bigtable zone is managed by a primary process, which balances workload and data volume within clusters
* This process splits busier/larger tablets in half and merges less-accessed/smaller tablets together, redistributing them between nodes as needed
* If a tablet gets a spike in traffic, Bigtable splits the tablet in two, then moves one of the new tablets to another node
* Distribute writes as evenly as possible across nodes
* Use row keys that do not follow a predictable order
