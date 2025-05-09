# [Waiting for Postgres 18: Accelerating Disk Reads with Asynchronous I/O](https://pganalyze.com/blog/postgres-18-async-io)
* Postgres historically operated under a synchronous I/O model
  * Every read request was a blocking system call
  * Database must pause and wait for the operating system to return data before continuing
  * In cloud environments, storage is often network-attached and I/O can have over 1 ms of latency
* Async I/O requests are scheduled to be read into a memory location, and the program waits for the completion of those reads instead of issuing each read individually
* `io_method = worker` utilizes dedicated I/O worker processes running in the background that retrieve data independently of query execution
  * Main backend process enqueues read requests and the worker processes interact with the Linux kernel to fetch data
  * This data is delivered to shared buffers, without blocking the main process
  * Workers are shared across connections and databases
* `io_method = io_uring` is a shared ring buffer between Postgres and the kernel
  * Eliminates the need for I/O worker processes
* When the backend process no longer blocks disk reads, the time spent doing I/O may not be reflected in `EXPLAIN ANALYZE` output
