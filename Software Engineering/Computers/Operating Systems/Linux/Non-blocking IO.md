# [Nonblocking I/O](https://copyconstruct.medium.com/nonblocking-i-o-99948ad7c957)

* A process references I/O streams with the help of descriptors, also known as file descriptors
  * Pipes, files, event queues are all examples of I/O streams referenced by a descriptor
* Descriptors are either created explicitly by system calls like `open`, `pipe`, `socket`, etc
* Descriptors are released when the process exits, by calling the `close` system call, after an `exec` when descriptor is marked as `close on exec`
* When a process forks, all the descriptors are duplicated in the child process
  * If any of the descirptors are marked `close on exec`, then after the parent forks, but before the child process `execs` the descriptors in the child process are closed
* Every descriptor points to a data structure called the file entry in the kernel
  * The file entry maintains a per descriptor file offset in bytes, from the beginning of the file entry object
  * An `open` system call creates a new file entry
* After a `fork` system call, both the parent and child processes use the same descriptor and reference the same offset in the file entry
* Multiple descriptors can reference the same file entry
  * The file entry data structure maintains a file offset for every descriptor
  * Read and write operations begin at this offset and the offset itself is updated after every data transfer
  * The offset determines the position in the file entry where the next read or write will happen
  * When a process terminates, the kernel reclaims all descriptors in use by the process
* All file descriptors expose an API that indicates operations, like read and write
  * The implementation of these operations varies by file type
  * This is why a file entry contains the `type` of file and an array of function pointers that translates this "generic" API for file operations to specific file-type functions
* A socket is referenced by a descriptor and acts as an endpoint for communication
  * Two processes can create two sockets each and establish a reliable byte stream by connecting those two end points
  * Once the connection has been established, the descriptors can be read from or written to using the file offsets described
  * The kernel can redirect the output of one process to the input of another on another machine

## Non-Blocking Descriptors

* By default, `read`, `write`, `send` all block on any descriptor if there's no data available
  * Exception is disk files, since writes to disk never happen directly but via the kernel buffer cache as a proxy
* Any descriptor can be put in non-blocking mode
  * An I/O system call will return immediately and the returned value will be an error, a partial count (when the input or output operation can be partially completed), or the entire result
* A descriptor is considered "ready" if a process can perform an I/O operation on the descriptor without blocking

### Level-Triggered

* To determine if a descriptor is ready, the process tries to performa a non-blocking I/O operation
* A process may perform such operations any number of times
* This allows for more flexibility with respect to the handling of any subsequent I/O operation

### Edge-Triggered

* Process receives a notification only when the file descriptor is "ready"
* A "push" model, in that a notification is "pushed" to the process about the readiness of a file descriptor
* No additional information like how many bytes arrived on a socket buffer
* Process can attempt to perform the maximum amount of I/O it possibly can every time it gets a notification because it has to wait until next notification (even if I/O is possible on descriptor before arrival of next notification)
* Scenario - there is a byte stream stored in a buffer that contains 1024 bytes available for reading
  * Process gets notification and reads 500 bytes
  * There are a remaining 524 bytes but process can only perform I/O once it gets the next notification, so 524 bytes are in the buffer until the next notification occurs
  * Once next notification is received, an additional 1024 bytes have arrived in the buffer - so now it's total size is 1548 bytes
* While it may be tempting to performa all the I/O immediately, a large I/O operation on a single descriptor has the potential to starve other descriptors
