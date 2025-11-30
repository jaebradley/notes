# [What are memory mapped page and anonymous page?](https://stackoverflow.com/a/29402385/5225575)
* There are four combinations: private file mapping, shared file mapping, private anonymous mapping, shared anonymous mapping
* File mapping specifies a file, on disk, that has N many bytes mapped into memory
* Typical process of using `mmap` to create a memory-mapped file is
  * `open` the file to get a file descriptor
  * `fstat` the file to get the size from the file descriptor data structure
  * `mmap` the file using the file descriptor from `open`
  * `close` the file descriptor
* When a file is mapped as `PRIVATE` , changes made are not committed to the underlying file
  * Changes to `SHARED` mapped files are committed to the underlying file by the kernel
    * Can be used for interprocess communication
* Anonymous mappings are not backed by a file
  * Can also use `/dev/zero` as the file
  * "Anonymous" is a bit of a misnomer - the "anonymous" comes from the fact that there isn't a specified file
* Example of anonymous private mappings are the heap and stack segments of a process
