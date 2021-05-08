# [Blocking I/O And Non-Blocking I/O](https://medium.com/coderscorner/tale-of-client-server-and-socket-a6ef54a74763)

* When a client makes a request to a server, and the server processes the request and sends back a response, both the client and the server need to establish a connection
  * Both the client and the server have to bind themselves to a socket at the end of its connection to its counterpart

## Blocking I/O

* With blocking I/O, when a client makes a request to connect with the server, the thread that handles that connection is blocked until there is some data to read, or the data is fully written
  * Until the relevant operation is complete, that thread can do nothing else but wait
  * To fulfill concurrent requests, need to have multiple threads
* On the server-side, create a new server socket to listen to requests connections on a specific port
* Call `accept` method - server starts to wait for the client to make a connection and when a client makes a request, the server socket accepts the connection from the client and returns a new socket to communicate with the client
  * Server socket continues to listen to client connections even after a new socket is created to communicate with a client
* Input and output streams are read / written from the socket
* To handle multiple concurrent users, we would need to allocate a new thread for each client socket
* Each thread requires a new stack of memory allocated to it, so spawning multiple threads and switching between them has a cost
* At any given point in time, there can be multiple threads waiting for client requests / idle which is a waste of resources

## Non-Blocking I/O

* Use a single thread to handle multiple concurrent connections
* In NIO-based systems, instead of writing data onto output streams and reading data from input streams, read / write data from buffers
  * Buffer is a temporary storage place
* "Channel" is the medium that transports bulk of data into / out of buffers and it can be viewed as endpoint for communication
* Java NIO has a class called "Selector" that allows a single thread to examine I/O events on multiple channels
  * That is, this selector can check the readiness of a channel for operations, like reading and writing
  * Different channels can be registered with a selector, and different operations can be specified that you are interested in observing
  * Each of these channels are assigned a "selection key" which serves as a pointer to a channel
* In Java-land, the operations are
  * Create a new Server Socket Channel
  * Bind the server socket channel to a particular host and port
  * Register a selector with the server socket channel, and tell the selector to listen for certain types of events
  * Poll the selector to see if there are any channels that need processing
  * Once there are channels that are "ready", loop through each channel and perform the necessary operations
* Only one thread, the main thread, processes multiple simultaneous connections

