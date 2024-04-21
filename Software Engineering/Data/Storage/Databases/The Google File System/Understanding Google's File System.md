# [Understanding Google’s File System](https://www.micahlerner.com/2020/03/22/understanding-googles-file-system.html#:~:text=What%20are%20the%20abstractions%20that,unique%20ids%20for%20a%20chunk.)

## What are the abstractions that power GFS?

* Chunks split up files into fixed-size 64MB segments
* These segments are replicated within a datacenter
* Each chunk has a "handle" aka a unique identifier
* This chunking approach allows handling many readers and writers of a single file
  * Allows huge files to exist behind a simple abstraction
  * "Opening a file" => fetch all the different chunks in the datacenter, and then (transparently) stitch them together for use (as if the data existed on your local filesystem)

### Components

#### Leader

* Communicates with a set of chunk servers
* In-memory tables that map
  * Filenames to chunk handles
  * Chunk handles to the machines that the chunk is on
    * Stores chunk version information (to help manage multiple writes to the same chunk)
    * Additional information for writing to a specific chunk (the primary and lease)

#### Chunk Servers

* These servers handle the actual work of writing data to and reading from disk
* The leader coordinates when this work happens

### Reading from GFS

* Client tells the leader "I would like to read this byte offset in this file"
* Leader calculates which chunk corresponds to the byte offset + file
* Leader then identifies which chunk servers store the chunk, and lets the client know
* Client chooses a chunk server, calls the chunk server with the chunk + offset it wants, and is given the information
* Client caches information about the chunk + chunkservers in case it needs to rerequest that chunk

### Writing from GFS

* Client asks leader for a file's last chunk
  * End of file is necessary because writes are append-only (i.e. no random writes)
* Leader identifies the file's last chunk handle
* Leader inspects the primary and lease data associated with the chunk
* The "primary" data is the chunk server that has been assigned to coordinate writes among chunk servers
  * Short-lived, and is governed by the expiration of the lease
  * When the lease expires, the leader can assign a new chunk server to coordinate writes
* If a chunk that a client requests does not have a primary chunk server assigned to it, the leader elects one
  * The leader increments the version of the data, allowing the leader to keep track of which data is most recent
    * If the chunk already has an assigned primary chunk server, this step is skipped
* Leader informs the client about the primary and secondary chunk servers
* The client sends the data it wants to write to the primary and secondary chunk servers
* After all chunk servers have the data, the client tells the primary to write the data
* The primary chunk server chooses a byte offset in the chunk, sends the byte offset to the secondary chunk servers
  * Primary and secondary chunk servers then perform the write
* If all the primary and secondary chunk servers successfully perform the write, the client receives a success message
* If not all secondary chunk servers successfully perform the write, the client receives a failure message
  * Client needs to talk to the leader again, and repeat the process from the beginning
