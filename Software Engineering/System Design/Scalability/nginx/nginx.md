# [nginx](https://www.aosabook.org/en/nginx.html)

## Why Is High Concurrency Important

* Imagine an Apache-based web server which produces a small response (100KB)
* Takes a fraction of a second to generate / retrieve response but can take 10 seconds to transmit it to a client with a bandwidth of 10KB/s
* Imagine 1,000 simultaneously connected clients
  * If only 1MB of additional memory is allocated per client, it would result in ~1GB in extra memory, devoted to serving 1000 clients, 100KB of content
  * In reality, typical web server based on Apache commonly allocates more than 1MB of additional memory per connection

### Isn't Apache Suitable

* The downside to having such a rich and universal combination of tools in a single piece of software is less scalability because of increased CPU and memory usage per connection
* nginx is event-based, and does not follow Apache's style of spawning new processes or threads for each web page request

## Overview of nginx Architecture

* Traditional process/thread based models of handling concurrent connections involve handling each connection with a separate process or thread and blocking on network or i/o operations
* Spawning a separate process or thread requires preparation of a new runtime environment, allocation of heap and stack memory and the creation of a new execution context
* Additional CPU time is also spent creating these items, which can eventually lead to poor performance due to thread thrashing on excessive context switching
* nginx uses multiplexing and event notifications and dedicates specific tasks to specific processes
* Connects are processed in a highly efficient run-loop in a limited number of single-threaded processes called workers
* Within each worker, nginx can handle many thousands of concurrent connections and requests per second

### Code Structure

* nginx worker code includes core and functional modules
* The core module is responsible for maintaining a tight run-loop and executing other modules' code at appropriate stages of request processing
* The usage of different methods for multiplexing and advanced I/O operations is heavily optimized for every Unix-based operating system nginx runs on

### Workers Model

* Worker processes accept new requests from a shared "listen" socket and execute a run-loop inside each worker
* On startup, an initial set of these listening sockets are created
* Run-loop tries to be as non-blocking as possible - only situation where nginx can block is when there's not enough disk storage performance for a worker process
* Instead of forking a process, nginx checks the state of network / storage and initializes new connections, adding them to the run-loop, processing them asynchronously until completion, and then deallocating / removing the connection from the run-loop
* A separate worker per core allows full utilization of multicore architectures
  * There is no resource starvation and the resource controlling mecahnisms are isolated within single-threaded worker processes
  * More scalability across physical storage devices, facilitates more disk utilization and avoids blocking on disk I/O

### Brief Overview of nginx Caching

* Caching is implemented as a hierarchical data store on a filesystem
* Cache keys and cache metadata are stored in shared memory segments
* No in-memory caching of files
* Each cached response is placed on a different file on the filesystem
* When a response is written to the cache directory, the path and name of the file are derived from an MD5 hash of the proxy URL
* When nginx reads the response from an upstream server, the content is first written to a temp file outside of the cache directory structure
* When nginx finishes processing the request, it renames the temp file and moves it to the cache directory

## nginx Internals

* nginx processes connections through a pipeline / chain of modules
* For every operation (compression, talkking to memcached) there is a module
* Typical HTTP request processing cycle:
  * Client sends HTTP request
  * nginx core chooses the appropriate phase handler based on configured location matching request
  * If configured to do so, load balancer picks an upstream server for proxying
  * Phase handler does its job and passes each output buffer to the first filter
  * First filter passes the output to the second filter (and so on)
  * Final response is sent to the client
* Module invocation is done through a series of callbacks using pointers to the executable functions
* When nginx handles an HTTP request, it passes the request through a number of processing phases
* At each phase there are handlers to call (phase handlers)
  * Phase handlers typically get the location configuration, generate an appropriate response, send the header, and send the body
  * They take one argument, a specific data structure describing the request (request method, URI, haeader, etc)
* Filters follow a Unix pipeline-like design pattern
  * Filter gets called, starts working, and calls next filter, etc etc until final filter in the chain of filters is called
  * Filters don't have to wait for the previous filter to finish - the next filter in the chain can start working as soon as the input from the previous one is available
  * Output response being generated ban be passed to the client before the entire response from the upstream server is received
* Header and body filters
  * Body filters transfer the generated content (like charset modification, gzip compression, chunked encoding)
* Subrequests can return results from a different URL than the one the client originally request (an internal redirect)
* Filters can perform multiple subrequests and combine the outputs into a single response
* Subrequests can also be nested and hierarchical (subrequest can perform own subrequest, etc)
* Example is the server-side include module, which uses a filter to parse the contents of the returned document and then replace the `include` directives with the contents of the specified URLs
* For each connection, the necessary memory buffers are dynamically allocated, linked, used for storing / manipulating the header and body and then freed upon connection release
  * Avoids copying data in memory and most of the data is passed along via pointers
* On an idle keepalive connection, nginx spends 550 bytes of memory
