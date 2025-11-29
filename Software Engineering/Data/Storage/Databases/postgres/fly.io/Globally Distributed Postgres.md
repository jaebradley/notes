# [Globally Distributed Postgres](https://fly.io/blog/globally-distributed-postgres/)
* Approach is to try and write data and when the write fails (due to trying to write to a read replica) then the catch the thrown exception and return a response with a `replay` header that tells the HTTP server proxy to retry the request in the primary node's region
* They collect gossiped load data to predict which process has capacity
* Send request to the server running the specific process
  * Check to see if the process has capacity
  * If it doesn't, send response to edge server indicating that process does not have capacity and to try a different process
