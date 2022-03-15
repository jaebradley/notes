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

## Metadata

* Master stores three types of metadata: file and chunk namespaces, mapping from files to chunks, and locations of each chunk's replicas
* The namespaces and file-to-chunk mappings are persisted by logging mutations to an operation log stored on the master's local disk and replicated on remote machines
* Master asks each chunkserver about its chunks at master startup and whenever a chunkserver joins the cluster


### In-Memory Data Structures

* Master periodically scans entire in-memory state
  * Period scanning is used to implement chunk garbage collection, re-replication in the presence of chunkserver failures, and chunk migration to balance load and disk space usage across chunkservers
* Number of chunks and capacity of the whole system is limited by how much memory the master has
* Less than 64B of metadata for each 64 MB chunk
* Most chunks are full (except last chunk)
* File namespace data typically requires less than 64B per file because file names are stored compactly using prefix compression

### Chunk Locations

* A chunkserver has the final word over what chunks it does (or does not) have on its disks
* No point in trying to maintain a consistent view of the information on the master because errors on the chunkserver may cause chunks to vanish spontaneously (like a disk may go bad and be disabled)
* So master does not keep persistent record of which chunkservers have a replica of a given chunk
* It polls chunkserver at startup and then periodically thereafter

### Operation Log

* Operation log contains historical record of critical metadata changes
* Only persistent record of metadata and is the logical time line that defines the order of concurrent operations
* Must store operation log reliably and not make changes visible until metadata changes are made persistent
* Replicate it on multiple remote machines and respond to a client operation only after flushing the corresponding log record to disk locally and remotely
* Log batching occurs to reduce impact of flushing and replication on overall system throughput
* Master recovers file system state by replaying the operation log
* Master checkpoints its state whenever the log grows beyond a certain state
  * It recovers by loading the latest checkpoint from the local disk and replaying only the limited number of log records after that
* New checkpoints can be created without delaying incoming mutations
* Master switches to a new log file and creates the new checkpoint in a separate thread
* New checkpoint contains all mutations before the switch
* Once the new checkpoint is completed, it is written to disk locally and remotely
* Recovery only needs the latest complete checkpoint and subsequent log files

## Consistency Model

### Guarantees by GFS

* File namespace mutations like file creation are atomic and are handled exclusively by the master
* A file region is consistent if all clients will always see the same data, regarldess of which replicas they read from
* A file region is defined if after a data mutation the file region is consistent and clients will see what the mutation writes in its entirety
  * When a mutation succeeds without interference from concurrent writers, the affected file region is defined
* Concurrent successful mutations leave the region undefined but consistent
  * All clients see the same data, but it may not reflect what any one mutation has written
  * Typically consists of mingled fragments from multiple mutations
* A failed mutation makes the region inconsistent - different clients may see different data at different times
* Data mutations are writes or record appends
* Writes cause data to be written at an application-specified file offset
* A record append causes data to be appended atomically at least once (even in the presence of concurrent mutations) but at an offset of GFS's choosing
* The offset is returned to the client and marks the beginning of a defined region that contains the record
* GFS may insert padding or record duplicates in between that occupy regions considered to be inconsistent
* After a sequence of successful mutations, the mutated file region is guaranteed to be defined and contains the data written by the last mutation
  * GFS applies all mutations to a chunk in the same order on all its replicas
  * Uses chunk version numbers to detect any replica that has become stale because it has missed mutations while its chunkserver was down
  * Stale replicas are never involved in a mutation or given to clients asking the master for chunk locations and are garbage collected at the earliest opportunity
* Since clients cache chunk locations, they may read from a stale replica before that information is refreshed
  * This window is limited by the cache entry's timeout and the next open of the file, which purges from the cache all chunk information for that file
  * As most of our files are append-only, a stale replica usually returns a premature end of chunk, rather than outdated data
  * When a reader retries and contacts the master, it will immediately get current chunk locations

### Implications for Applications

* All Google's applications mutate files by appending rather than overwriting
* In one typical use, a writer generates a file from beginning to end
  * Periodically checkpoints how much has been successfully written
  * Checkpoints may also include application-level checksums
  * Readers verify and process only the file region up to the last checkpoint, which is known to be in the defined state
  * Checkpointing allows writers to restart incremenetally and keeps readers from processing successfully written file data that is still incomplete from the application's perspective
* Other typical use case involves many writers concurrently appending to a file for merged results
  * Record append's append-at-least-once semantics preserves each writer's output
  * Readers deal with the occassional padding and duplicates by
    * Using the checksum that is attached to each record, readers can identify and discard extra padding and record fragments using the checksums
    * If readers cannot tolerate occassional duplicates (i.e. downstream operations are not idempotent), it can filter them out using unique identifiers in the records
