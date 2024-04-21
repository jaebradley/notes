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





