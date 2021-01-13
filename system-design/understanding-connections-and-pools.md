# [Understanding Connections & Pools](https://sudhir.io/understanding-connections-pools/0

* When you run a program, the operating system runs your code as one instance of a process
  * A process occupies one CPU core and some memory when it's running and does not share its memory with any other processes
  * The process can start threads, which are like children of the process that can run concurrently
  * Threads share memory with the process that spawned them and might allocate more memory for their use
  * The process might use an event loop, which is a single-process system that keeps track of tasks it has to do and loops over all its tasks continuously and infinitely, each time doing the tasks that it can, or skipping them if they're blocked

## Connection-Handling Architectures

* Processes - each connection is handled by a separate process, either started exclusively for the connection (Postgres) or maintained as a part of a group of available processes (Unicorn)
* Threads - each connection is handled by a separate thread, either spawned exclusively for the connection or held in reserve after spawning
  * The threads might be spread over multiple processes
* Event loop - each connection is a task in the event loop, and connections that have data to be read are processed by iterating over these connections
  * Normally single-process and single-threaded
* Coroutines / Fibers - Each connection is handled by a lightweight construct whose scheduling is managed internally (Go)

## Why a Pool?

* A pool maintains a set of connections internally
 * Connections are given out by the pool when communication with the database needs to occur and returned to the pool when the communication is finished
 * Ideal use of a connection pool is that logic requests a connection from the pool (called a checkout) and then releases the connection back into the pool right after it is done using the connection
   * Hopefully, there isn't logic holding onto the connection while non-connection related work is being done
   * This allows many pieces of work that need a connection to accomplish their tasks while using a few connections in total
* In Ruby on Rails, if there is a database request, followed by a long network request to another service, and then another database request, the database connection would have been idle while the network request was happening
* Go releases connections between database calls so subsequent operations in the same request may not run on the same connection
* Databases often base transaction functionality on a connection (session)
  * Can only commit or rollback a transaction on the same connection that it was started on
  * Automatic pool management needs to be careful to not release connections that are in the midst of a transaction

## Internally Managed / Custom Handling

* In Go, every incoming request is handled by a new goroutine, a lightweight thread-like construct that the Go runtime internally manages and schedules much more efficiently than threads or processes
* Go will automatically spread its goroutines across all the CPU cores you have
* If each goroutine is using the standard SQL library package, it will create as many connections as there are goroutines because the default pool size is unlimited
  * So make sure to configure the connection limit (`SetMaxOpenConns`)
  * Each executed query will checkout a connection from the pool and immediately release it back
  * If you want to execute a transaction, must use the API to wrap the connection to commit / rollback the transaction
