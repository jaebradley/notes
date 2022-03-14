# Design Overview

## Assumptions

* System is built from inexpensive commodity components that often fail
  * It must constantly monitor itself and detect, tolerate, and recover promptly from component failures on a routine basis
* The system stores a modest number of large files (few million files, between 100MB or larger)
  * Multi-GB files are a common case
  * Small files are supported but do not need to be optimized
* Workload consists of two types of reads
  * Large streaming reads and small random reads
  * Large streaming individual read operations typically read on the scale of MBs
  * Successive operations from same client typically read through same contiguous region of a file
  * Small random read typically reads a few KB at some arbitrary offset
* Multiple clients might be appending to the same file so atomicity with minimcal synchronization overhead is essential

## Architecture

* Cluster consists of single master, multiple chunkservers
* Clients are typically run on commodity Linux machines that execute a user-level server process
* Files are divided into fixed-size chunks
* Each chunk is identified by an immutable and globally unique 64-bit chunk handle
* Chunkservers store chunks on local disks as Linux files and read or write chunk data specified by a chunk handle and byte range
* Each chunk is replicated on multiple chunkservers (default, 3 replicas)
* Master maintains all file system metadata
  * Namespaces, access control information, mapping from files to chunks, and current locations of chunks
  * Control system-wide activities like chunk lease management, garbage collection of orphaned chunks, and chunk migrations between chunkservers
  * Master periodically communicates with each chunkserver in HeartBeat messages to give it instructions and collect its state
* Clients interact with the master for metadata operations but all data-bearing communication goes directly to the chunkservers
* Clients nor chunkservers cache file data
  * Client caches don't make sense because most applications are streaming through huge files / working sets are too large to be cached
  * Clients cache metadata
  * Chunkservers do not need a cache because chunks are stored as local files and so Linux's buffer cache is already keeps frequently accessed data in memory

## Single Master

* Clients never read and write file data through the master
* Clients ask master about which chunkservers it should contact
* Clients cache this information for a limited time and interact with the chunkservers directly for subsequent operations
* Example of simple read request
  * Client sends request to master containing file name and chunk index (chunk index is calculated using the fact that each chunk size is fixed and the byte offset to start reading from)
  * Master replies with corresponding chunk handle and locations of replicas
    * Client caches this information
  * Client picks closest replica and reads from the replica using the chunk handle and a byte range within the chunk to read from
  * Master is no longer needed to read from the same chunk until the cached replica locations expire

## Chunk Size

* 64 MB is the chunk size, which is much larger than typical file system block sizes
* Large chunk size reduces clients' interactions with the master because reads and writes on the same chunk require only one initial request to the master for the chunk location information
* On a large chunk, clients are more likely to perform many operations on a given chunk, reducing network overhead by keeping a persistent TCP connection to the chunkserver over an extended period of time
* Reduces size of metadata stored on master, allowing metadata to be kept in memory
* A small file consists of a small number of chunks (maybe even 1)
  * The chunkservers storing these chunks may become hot spots if many clients are accessing the same file
