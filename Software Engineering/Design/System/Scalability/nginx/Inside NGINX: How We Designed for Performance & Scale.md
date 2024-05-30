# [Inside NGINX: How We Designed for Performance & Scale](https://www.f5.com/company/blog/nginx/inside-nginx-how-we-designed-for-performance-scale)

* NGINX has a leader process and worker/helper processes
* The leader process performs special actions (reading config, binding to ports)
* Leader process also creates the worker/helper processes
  * In article example, leader process creates four worker processes and two cache helper processes on four-core server
  * One worker process per core (which is the default recommended setting)
  * Reduces the amount of context switching that happens
* Common to design network-based applications by assigning a thread / process to each connection
  * This approach does not scale well when application needs thousands of simultaneous connections
* When an NGINX server is active, only the worker processes are busy
* Worker processes are single-threaded, and run independently of each other
  * They can communicate using shared memory (shared cache data, shared session data, etc)
* NGINX worker process is initialized and provided a set of listen sockets by the leader process
* Worker processes wait for events on these listen sockets
* Incoming connections are assigned to a state machine (like an HTTP state machine)
* Worker process is like a chess grandmaster playing a simul
  * Worker waits for events on the listen and connection sockets
  * When events occur on the listen socket, a client has started a new connection and the worker creates a new connection socket
    * Each new connection is represented using a file descriptor and a small amount of additional memory
  * When events occur on a connection socket that means that the client has "made a new move"
* Worker never blocks on network traffic, and waits for the client to respond
* After worker responds, worker proceeds to any events that need to be processed
