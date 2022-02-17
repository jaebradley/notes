# [HDFS Architecture Guide](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html)

## Assumptions and Goals

* Hardware failures are the norm rather than the exception
* An HDFS instance may consist of hundred or thousands of server machines
* Emphasis on high throughput of data access rather than low latency of data access
  * HDFS is designed more for batch processing rather than interactive use by users
* A typical file in HDFS is gigabyte to terabytes in size
  * Scale to hundreds of nodes in a single cluster
  * Tens of millions of files in a single instance

## NameNode and DataNodes

* HDFS cluster consists of a single NameNode, a master server that manages the file system namespace and regulates access to files by clients
  * NameNode executes file system namespace operations like opening, closing, renaming files and directories
  * Determines mappong of blocks to DataNodes
* Many DataNodes, one DataNode per cluster, which manage storage attached to the nodes that they run on
  * DataNodes perform block creation, block deletion, and block replication upon instruction from the NameNode
* HDFS exposes a file system namespace
  * Internally, files are split into one or more blocks, and these blocks are stored in a set of DataNodes

## Data Replication

* HDFS is designed to reliably store very large files across machines in a large cluster
* It stores each file as a sequence of blocks
* All blocks in a file except the last block are the same size
* The blocks of a file are replicated for fault tolerance
* Files in HDFS are written once

## Replica Placement: The First Baby Steps

* Optimizing replica placement distinguishes HDFS from other distributed file systems
* Large HDFS instances run on a cluster of computers that are commonly spread across many racks
* The NameNode determines the rack id each DataNode belongs to
* A naive and non-optimal policy is to place replicas on unique racks
  * Prevents data loss if an entire fact fails
  * Increases cost of writes because a write needs to transfer blocks to multiple racks
* For a replication factor of 3, HDFS puts one replica on one node in the local rack, another replica on a node in a different (remote) rack, and the last replica on a different node in the same remote rack

## Replica Selection

* To minimize global bandwidth consumption and read latency, HDFS tries to satisfy read requests from a replica that is closest to the reader

## The Persistnce of File System Metadata

* HDFS namespace is stored by the NameNode
* NameNode uses transaction log called EditLog to peristently record every change that occurs to file system metadata
  * Creating a new file or changing the replication factor of a file causes a new record to be inserted into the EditLog
  * NameNode uses a file in local host OS file system to store the EditLog
* The entire file system namespace, including the mapping of blocks to files and the file system properties is stored in a file called the FsImage - FsImage is also stored as a file in the NameNode's local file system
* The NameNode keeps an image of the entire file system namespace in memory
  * This in memory view is populated by the FsImage and EditLog files (on disk)
  * When the NameNode starts up, NameNode applies all the transactions from the EditLog to the in-memory representation of the FsImage, and flushes out this new version into a new FsImage on disk
  * NameNode then truncates the old EditLog because its transactions have been applied to the persistent FsImage
* DataNode stores HDFS data in files in its local file system
  * It stores each block of HDFS data in a separate file in its local filesystem
  * DataNode does not create all files in the same directory - it uses some heuristic to determine optimal number of files per directory and creates subdirectories appropriately
  * Not optimal to create all local files in a single directory because the local file system might not be able to efficiently support a huge number of files in a single directory
* When a DataNode starts up, it scans through its local file system, generates a list of all HDFS data blocks that correspond to each of these local files and sends the report to the NameNode

## Robustness

### Data Integrity

* The HDFS client software implements checksum checks on the contents of the HDFS files
* Client computes checksum of each block of the file when it creates an HDFS file and stores checksums to validate later
* When it retrieves a file's contents, it verifies that the data it received from each DataNode matches the checksum stored in the associated checksum file

### Metadisk Disk Failure

* NameNode can support maintaining multiple copies of the FsImage and EditLog files
  * An update to either of these files causes each of the copies to also get updated synchronously, which can impact the number of namespace transactions per second that a NameNode can support
* NameNode is a single point of failure for an HDFS cluster

## Data Organization

### Data Blocks

* Typical block size is 64 MB, so an HDFS file is chopped into 64 MB chunks and if possible, each chunk resides on a different DataNode

### Staging

* When a file is created, the HDFS client caches the file data into a temporary local file
* Application writes are redirected to this temporary file
* Once the file accumulates data worth over one HDFS block size, the client contacts the NameNode
* NameNode inserts the file name into the file system hierarchy and allocates a data block for it
* Client flushes the block of data from the local temporary file to the specified DataNode
* Once the client tells the NamdeNode that the file is closed, the NamdeNode commits the file creation operation into persistent store
* If the NameNode dies before the file is closed, the file is lost

### Replication Pipelining

* When the client flushes the data block to the first DataNode, the first DataNode starts receiving the data in small 4kb sections, writes each section to its local repository and transfers that portion to the second DataNode, which writes each section to its local repository and transfers that portion to the third DataNode

## Space Reclamation

### File Deletes and Undeletes

* When a file is deleted, HDFS first renames it to a file in the `/trash` directory
* A file remains in `/trash` for a configurable amount of time, after which, the NameNode deletes the file from the HDFS namespace
  * Default policy is to delete friles from `/trash` that are more than six hours old
* When the file is deleted from the HDFS namespace, this causes the blocks associated with the file to be freed
