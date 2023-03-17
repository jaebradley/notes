# [Bigtable: A Distributed Storage System for Structured Data](https://static.googleusercontent.com/media/research.google.com/en//archive/bigtable-osdi06.pdf)

## Data Model

* A sparse, distributed, persistent multi-dimensional sorted map
* Map is indexed by a row key, column key, and a timestamp
  * Can think of this as a tuple of `(row: string, column: string, time: int64)` mapping to a `string
  * Example is keeping a copy of a large collection of web pages (`Webtable`)
  * In `Webtable` they would use URLs as row keys (`com.cnn.www`), varous aspects of the web pages as column names, and store the contents of the web pages in the `contents` column
  * The `contents` column would have multiple versions, each associated with the timestamps when the contents were fetched
* Each value in the map is an uninterpreted array of bytes

## Rows

* Row keys in tables can be arbitrary strings
* Every read or write of data under a single row key is atomic
* Row keys are maintained in lexicographical order
* The row range for a table is dynamically partitioned
* Each row range is called a tablet, the unit for distribution and load balancing
* Reads of short row ranges are efficient and typically require communication with only a small number of machines
* For `Webtable`, pages in the same domain are grouped together into contiguous rows by reversing the hostname components of the URLs
  * `maps.google.com/index.html` is stored under the key `com.google.maps/index.html` so that it is stored near rows that share the same domain

## Column Families

* Column keys are grouped into sets called column families
* All data in a column family is usually of the same type
* A column family must be created before data can be stored under any column key in that family
* Distinct number of column families should be small (hundreds at most)
  * There can be many many columns
* A column key has the following syntax: `family:qualifier`
* `Webtable` uses an `anchor` column family where each column key in the family represents a single anchor
  * The qualifier is the name of the referring site and the cell contents is the link text
  * CNN's home page is referenced by both Sports Illustrated and MY-look home page so for the CNN row, there are two columns in the `anchor` column family: `anchor:cnnsi.com` and `anchor:my.look.ca`

## Building Blocks

* Bigtable uses the distributed Google File System to store log and data files
* The Google SSTable file format is used to store Bigtable data
  * SSTables provide a persistent ordered immutable map from keys to values (keys and values are arbitrary byte strings)
* Internally, each SSTable contains a sequence of blocks (typically ~64kb)
* Block index at end of SSTable is used to locate blocks
* Block is loaded into memory when SSTable is opened
* A lookup can be performed with a single disk seek: binary search the in-memory index to find the location of the appropriate block, and then read the block from disk
* An SSTable can also be completely mapped into memory to avoid touching disk
* Distributed lock service named Chubby

## Implementation

* Bigtable has three components: a client library, a master server, and many tablet servers
* Tablet servers are dynamically added / removed from cluster
* Master server is responsible for assigning tablets to tablet servers, detecting the addition and expiration of tablet servers, balancing tablet server load, and garbage collecting files in GFS
  * Also handles schema changes like table and column family creations
* Each tablet server handles a set of tablets, managing reads and writes to the tablets, and splitting tablets that have grown too large
* Client data does not move through the master: clients communicate directly with tablet servers for reads and writes

### Tablet Location

* Three-level hierarchy analogous to that of a B+ tree to store tablet locatio information
* First level is a file stored in Chubby that contains the location of the root tablet
  * Root tablet contains the location of all tablets in a special METADATA table
  * Each METADATA tablet within the METADATA table contains the location of a set of user tablets
  * Root tablet is the first tablet in the METADATA table, but unlike other tablets it is never split
* The METADATA table stores the location of a tablet under a row key that is an encoding of the tablet's table identifier and its end row
* Client library caches tablet locations
  * If the client does not know the location of a tablet, or the cached location information is incorrect, then it recursively moves up the tablet location hierarchy
* If the client's cache is empty, it requires three network round-trips, including one read from Chubby
* If the client's cache is stale, it requires up to six round-trips because stale cache entries are only discovered on misses
* The client library prefetches tablet locations and reads the metadata for more than one tablet whenever it reads the METADATA table

### Tablet Assignment

* * Each tablet is assigned to one tablet server at a time
* The master keeps track of the set of live tablet servers and the current assignment of tablets to tablet servers, including which tablets are unassigned
* When a tablet is unassigned, and a tablet server with sufficient room for the tablet is available, the master assigns the tablet by sending a tablet load request to the tablet server
* Bigtable uses Chubby to keep track of tablet servers
* When a tablet server starts, it creates and acquires an exclusive lock on a uniquely-named file in a specific Chubby directory
* The master monitors this directory to discover tablet servers
* A tablet server stops serving its tablets if it loses its exclusive lock
  * A tablet server will attempt to reacquire an exclusive lock on its file as long as the file still exists
* If the file no longer exists, then the tablet server will never be able to serve again, so it kills itself
* When a tablet server terminates, it attempts to release its lock so that the master will reassign its tablets more quickly
* The master is responsible for detecting when a tablet server is no longer serving its tablets, and for reassigning the tablets as soon as possible
* The master asks each tablet server periodically for the status of its lock
  * If a tablet server reports that it has lost its lock, or if the master was unable to reach a server during its last several attempts, the msater attempts to acquire an exclusive lock on the server's file
* If the master is able to acquire the lock, Chubby is live, and the tablet server is dead or having trouble reaching Chubby
  * The master ensures that the tablet server is can never serve again by deleting its server file
* Once the server file is deleted, master can move all the tablets that were previously assigned to the server to the set of unassigned tablets
* When a master is started, it needs to discover the current tablet assignments before it can change them
* The master grabs the unique master lock in Chubby, preventing concurrent master instantiations
* The master scans the servers directory in Chubby to find the live servers
* The master communicates with every live tablet server to discover what tablets are already assigned to each server
* The master scans the METADATA table to learn the set of tablets, and to identify the unassigned tablets

### Tablet Serving

* The persistent state of a tablet is stored in Google File System
* Updates are committed to a commit log, which is stored in GFS
* Recently committed ones are stored in memory in a sorted buffer called a memtable while older updates are stored in a sequence of SSTables
* Tablet recovery starts with the tablet server reading its metadata from the METADATA table
  * This metadata contains the list of SSTables that comprise a tablet and a set of redo points
  * Redo points are pointers into any commit logs that may contain data for the tablet
  * The server reads the indices of the SSTables into memory and reconstructs the memtable by applying all of the updates that have committed since the redo points
* When a valid write operation arrives at a tablet server, a valid mutation is written to the commit log
* After a write has been committed, its contents are inserted into the memtable

### Compactions

* Write operations execute and the memtable size increases
* When the memtable size reaches a certain threshold, the memtable is frozen, a new memtable is created, and the frozen memtable is converted into an SSTable and written to GFS
* This is called a "minor compaction" and is supposed to both shrink the memory usage of the tablet server, and reduces the amount of data that has to be read from the commit log during recovery if this server dies
* Every "minor compaction" creates a new SSTable
* A merging compaction reads the contents of a few SSTables and the memtable and writes out a new SSTable
  * The input SSTables and memtable are discarded after the compaction has finished
* A "major compaction" is a merging compact that rewrites all SSTables to exactly one SSTable
* Major compactions don't contain any deletion information or deleted data
  * Non-major compactions can contain special deletion entries that suppress deleted data in older SSTables that are still live

## Refinements

### Compression

* Two-pass custom compression scheme
  * First pass uses Bentley and McIlroy's scheme which compresses long common strings across a large window
  * Second pass uses a fast compression algorithm that looks for repetitions in a small 16kb window of the data
  * Achieved 10-to-1 reduction in space in Webtable when storing Web page contents (vs. typical Gzip reductions of 3-to-1 or 4-to-1 for HTML pages)
  * Since pages from a single host are stored close to each other, the Bentley-McIlroy algorithm is able to identify large amounts of shared boilerplate in pages from the same host

### Bloom Filters

* A read operation has to read from all SSTables that make up the state of a table
* If these SSTables are not in memory, many disk accesses may occur
* To reduce the number of accesses, Bloom filters are created for SSTables in a particular locality group
* Bloom filter are used to detect whether an SSTable might contain any data for a specific row/column pair

### Commit-log implementation

* A commit log file per tablet would imply a large number of files being written concurrently in GFS
* Append mutations to a single commit log per tablet server, co-mingling mutations for different tables in the same physical log file
* When a tablet server dies, the tablets that it served will be moved to a large number of other tablet servers
  * Each server typically loads a small number of the original server's tablets
* To recover the state for a tablet, the new tablet server needs to reapply the mutations for that tablet from the commit log written by the original tablet server
* To avoid duplicating reading the same physical log file multiple times (since the tablets have been distributed over a large number of machines) first need to sort the commit log entries by the combination of `table, row name, log sequence number`
* Post-sorting, all mutations for a particular tablet are contiguous and can be read efficiently with one disk seek followed by a sequential read

### Exploiting immutability

* Since SSTables are immutable, no need to synchronize file system accesses when reading from SSTables
* only mutable data structure that is accessed by both reads and writes is the memtable
  * Memtable row is copy-on-write which allows reads and writes to proceed in parallel
* Removing deleted data is solved by garbage-collecting obsolete SSTables
  * Each tablet's SSTables are registered in the METADATA table
  * The master removes obsolete SSTables using mark-sweep garbage collection using the METADATA table's set of root tables
