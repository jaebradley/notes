# [Envoy threading model](https://blog.envoyproxy.io/envoy-threading-model-a8d44b922310)
zsh:1: command not found: zx0fq

* Envoy has three different thread types
  * Main thread that coordinates critical process functionality
  * Worker threads
    * By default, Envoy spawns a worker thread for every hardware thread in the system
    * Each worker thread runs a non-blocking event loop that is responsible for listening on every listener, accepting new connections, instantiating a filter stack for the connection, and processing all IO for the lifetime of the connection
  * File flushing threads
    * Every file that Envoy writes (primary logs) currently has an independent blocking flush thread
    * This is because writing to filesystem cached files even when using `0_NONBLOCK` can sometimes block
    * When worker threads need to write to a file, the data is actually moved into an in-memory buffer, where it is eventually flushed via the file flush thread
  * This is one area of code in which technically all workers can block on the same lock trying to fill the memory buffer

## Connection handling 

* Once a connection is accepted on a worker, it never leaves that worker
  * All further handling of the connection is entirely processed within the worker thread, including any forwarding behavior
  * All connection pools in Envoy are per worker thread
    * So even though HTTP/2 connection pools only make a single connection to each upstream host at a time, if there are four workers, there will be four HTTP/2 connections per upstream host at steady state
    * By keeping everything within a single worker thread, almost all code can be written without locks and as if it was single threaded
    * Quite important to tune the `--concurrency` option. Having more workers than is needed will waste memory, create more idle connections, and lead to a lower connection pool hit rate

## Thread Local Storage

* A main thread process does some work, and then needs to update each worker thread with the result of that work, and without the worker thread needing to acquire a lock on every access
* Code running on the main thread can allocate a process-wide thread local storage slot
  * In practice, this is an index into a vector allowing O(1) access
* The main thread can set arbitrary data into its slot. After this is done, the data is posted into each worker as a normal event loop event
* Worker threads can read from their thread local storage slot and will retrieve whatever thread local data is available there
* Change only happens during quiet periods between work events

## Cluster update threading

* The cluster manager is responsible for creating an eventually consistent view of every upstream cluster which includes the discovered hosts as well as health status
* The health checker performs active health checking and reports health state changes back to the cluster manager
* xDS / DNS is performed to determine cluster membership and state changes are reported back tot he cluster manager
* Every worker thread is continuously running an event loop
* When the cluster manager determines that state has changed for a cluster, it creates a new read-only shapshot of the cluster state and posts it to every worker thread
* During the next quiet period, the worker thread will update the snapshot in the allocated thread local storage slot
* During an I/O event that needs to determine a host to load balance to, the load balancer will query the thread local storage slot for host information - no locks are required to do this

## Other subsystems that make use of Thread Local Storage

* The current feature flag override map is computed on the main thread and a read-only snapshot is then provided to each worker using RCU semantics
* Computing the HTTP date header on every request is quite expensive (~25k+ requests per second per core)
  * Envoy centrally computes the date header about every half second and provides it to each worker via Thread Local Storage and RCU
