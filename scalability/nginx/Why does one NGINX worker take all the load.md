# [Why does one NGINX worker take all the load?](https://blog.cloudflare.com/the-sad-state-of-linux-socket-balancing/)

* 3 ways of designing a TCP server with regards to performance
  * Single listen socket, single worker process
    * Processing limited to a single CPU
    * Single worker process is doing both accept() calls to receive new connections and processing the requests themselves
  * Single listen socket, multiple worker processes
    * New connections exist in a single kernel data structure (the listen socket)
    * Multiple worker processes do both the acceppt() calls and process the request
  * Multiple worker processes, each with separate listen socket
    * Using the SO_REUSEPORT socket option, can create a dedicated kernel data structure (the listen socket) for each worker process
    * Avoids listen socket contention
