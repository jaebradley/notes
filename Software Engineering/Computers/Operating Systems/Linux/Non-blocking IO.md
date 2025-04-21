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

## Multiplexing I/O on descriptors
* A process might want to handle I/O on more than one descriptor
* A program needs to log to stdout and stderr, while also accepting connections on a socket

## Multiplexing I/O with Non-Blocking I/O
* Put all the file descriptors in non-blocking mode
* Process attempts to perform the I/O operations on the descriptor, returning an error, partial output, for result of the I/O operation if it succeeds
* Cons:
* Process tries to perform I/O operations very frequently, and retry operations that returned an error to check if descriptors are ready
* Busy waiting in a tight loop burns CPU cycles
* If operations are conducted infrequently, then it might take a process an unacceptably long time to respond to an I/O event that is available
* Operations on output descriptors (writes) don’t generally block
* Try to perform the I/O operation first, revert back to polling when the operation returns an error
* Edge-triggered notifications: descriptors are put in non-blocking mode, process gets notified of an I/O event, process repeatedly tries I/O operations until the system calls block with an EAGAIN / EWOULDBLOCK

## Multiplexing I/O via Polling I/O
* Descriptors are put in non-blocking mode
* Process uses “level-triggered” mechanisms to ask the kernel by means of a system call (`select` or `poll`) which descriptors are capable of performing I/O

### Select
* `Select` monitors three sets of descriptors: `readfds` / `writefds` / `exceptfds` (monitors if read/writes are possible and monitors if exceptional conditions are met)
* When `select` returns, the descriptors are modified in-place to indicate which file descriptors actually changed status
* `select` `timeout` value
* `0` means non-blocking, returns immediately after polling file descriptors
* `NULL` will block forever. kernel can put the process to sleep until `select` returns
  * Select will block until 1+ of the descriptors are ready or the call is interrupted by a signal handler
* If an error occurred, `select` will return `-1`
* If the call timed out, return code is `0`
* If 1+ file descriptors are ready, return code is a positive integer indicating the ttoal number of file descriptors in all of the sets that are ready

### Poll
* `Poll` differs from `select` only in terms of how the tracked descriptors are specified
* Pass `poll` the set of descriptors, each marked with the events the caller wants to track
* `pollfd`: ID of the descriptor to poll, bit masks indicating what events to monitor for the descriptor, bit masks set by the kernel that indicate the events that actually occurred on the descriptor
* `timeout`
  * `-1`: `poll` blocks indefinitely, until one of the descriptors is ready
  * `0`: `poll` does not block, but returns immediately after polling to check if any of the file descriptors are ready
  * `1+`: `poll` blocks until the  timeout milliseconds or one of the file descriptors becomes ready, or a signal is caught

### What happens in the kernel?
* Both `select` and `poll` are stateless
* Every time a `select` or `poll` system call is made, the kernel checks every descriptor
* Cost of `poll` / `select` is O(# of descriptors being monitored)

### Cons
* Two system calls: `select` / `poll` to find out which descriptors are ready to perform I/O and another system call to do the actual operation (`read/write`)
* Descriptors are only monitorable for three events: reading, writing, exceptional conditions
* When the number of descriptors is large (like a web server handling thousands of mostly sleeping clients), the kernel still needs to scan every descriptor in the list and check for all the relevant conditions for the descriptor

## Kernel event polling on BSD
* Descriptors are put in non-blocking mode
* Process provided a generic notification interface known as `kevent`
* Allows monitoring of a wide variety of events, allowing these kernel event activities to be notified in a scalable manner
* For example: changes to file attributes when a file is renamed / deleted, posting of signals to it when it forks, execs or exits, asynchronous I/O completion
* Kernel only returns a list of the events that have occurred vs. returning the status of every event the process has registered
* Kernel builds the event notification structure one time only
* Especially good for when there are a large number of sleepy/slow clients since number of events to notify remains small even if the number of events that are being monitored is large

## Async I/O in POSIX
* POSIX defines a specification for parallel I/O, allowing a process to instantiate I/O operations without having to block or wait for any to complete
* I/O operation is queued to a file, and the process is later notified when the operation is complete. At this point, the process can retrieve the results of the I/O
* Linux exposes functions like `io_setup`, `io_submit`, `io_getevents`, `io_destroy` allowing submission of I/O requests from a thread, without blocking the thread
* This is ideal for disk I/O, especially random I/O on an SSD)
* Cons: maintaining multiple threads to perform I/O operations is expensive and scales poorly
* FreeBSD has POSIX AIO, implemented using the `aio` system call
* An async kernel process (kernel I/O daemon) performs the queued I/O operations
