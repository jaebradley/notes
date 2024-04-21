# [Understanding Google’s File System](https://www.micahlerner.com/2020/03/22/understanding-googles-file-system.html#:~:text=What%20are%20the%20abstractions%20that,unique%20ids%20for%20a%20chunk.)

## What are the abstractions that power GFS?

* Chunks split up files into fixed-size 64MB segments
* These segments are replicated within a datacenter
* Each chunk has a "handle" aka a unique identifier
* This chunking approach allows handling many readers and writers of a single file
  * Allows huge files to exist behind a simple abstraction
  * "Opening a file" => fetch all the different chunks in the datacenter, and then (transparently) stitch them together for use (as if the data existed on your local filesystem)