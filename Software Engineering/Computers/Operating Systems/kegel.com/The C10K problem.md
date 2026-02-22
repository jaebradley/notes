# [The C10K problem](https://www.kegel.com/c10k.html)
## 1. Serve many clients with each thread, and use nonblocking I/O and **level-triggered** readiness notification
* Set nonblocking mode on all network handles
* Use `select` or `poll` to tell which network handle has data waiting
* Kernel tells you whether a file descriptor is ready
  * And whether or not you've done anything with that file descriptor since the last time the kernel told you about it
  * Note that the readiness notification from the kernel is a *hint* - the file descriptor might not be ready anymore when you try to read from it
* Important bottleneck is that `read` or `sendfile` from disk blocks if the page is not in core at the moment
  * Setting nonblocking mode on a disk file handle has no effect
  * This is also true of memory-mapped disk files
* Without asynchronous I/O, the first time a server needs disk I/O, the process blocks, all clients must wait
* The ways in which a single thread can tell which set of nonblocking sockets are ready for I/O
  * `select`: limited to `FD_SETSIZE` handles
  * `poll`: slows down around a few thousand file descriptors
    * Most file descriptors are idle at any given time and scanning through thousands of file descriptors takes time
  * `/dev/poll`: Get an open handle to `/dev/poll` and tell the operating system what files you're interested in by writing to that file handle
    * Afterwards, read the set of currently ready file descriptors from that handle
    * `/dev/poll` use on Linux is *not* recommended
  * `kqueue`: is the recommended poll replacement for FreeBSD

## 2. Serve many clients with each thread, and use nonblocking I/O and readiness **change** notification
* Readiness change notification (edge-triggered readiness notification) means you give the kernel a file descriptor and later, when that descriptor transitions from `not ready` to `ready`, the kernel notifies you
  * Kernel assumes you know the file descriptor is ready and will not send any more readiness notifications of that type for that file descriptor
* When using readiness change notifications, you must be prepared for spurious events, since one common implementation is to signal readiness whenever any packets are received, regardless of whether the file descriptor was already ready
* `kqueue`: like `/dev/poll`, you allocate a listening object
  * To change the events you are listening to, or to get the list of current events, call `kevent()` on the descriptor returned by `kqueue()`
  * Can listen for socket readiness, plain file readiness, signals, I/O completion
* `epoll` : the recommended edge-triggered poll replacement for the 2.6 Linux kernel

## 3. Serve many clients with each server thread, and use asynchronous I/O
* Under standard unix, asynchronous I/O is provided by the `aio` interface
  * Interface associates a signal and a value with each I/O operation
  * Signals and their values are queued and delivered efficiently to the user process
* AIO is normally used with edge-triggered completion notifications i.e. a signal is queued when the operation is complete
  * Note that AIO does not provide a way to open files without blocking for disk I/O

## 4. Serve one client with each server thread
* This approach lets `read` and `write` block
* Uses a whole stack frame for each client, which is expensive from a memory perspective
* Many OS's have trouble handling more than a few hundred threads
  * If each thread has a 2 MB stack, you run out of virtual memory at 512 threads on a 32-bit machine with a 1GB user-accessible VM
