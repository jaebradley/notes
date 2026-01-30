# [Writing the Server Side of a Socket](https://docs.oracle.com/javase/tutorial/networking/sockets/clientServer.html)

* If the server successfully binds to its port, then the `ServerSocket` object is successfully created and the server continues on to the next step - accepting a connection from a client (`clientSocket = serverSocket.accept()`)
* The `accept` method waits until a client starts up and requests a connection on the host and port of this server
  * `accept` returns a new `Socket` object which is bound to the same local port and has its remote address and remote port set to that of the client accessing the server
  * The server can communicate with the client over this new `Socket` and can listen for client connection requests on the original `ServerSocket`
* To communicate with the client, get the socket's input / output streams and open readers and writers on them
  * Read / write to the socket using the readers and writers
* Make sure the input stream, output stream, client socket, and server socket are all created with try-with-resources to automatically close those resources
* When supporting multiple client requests, the server must accept connections sequentially (client connection requests are queued at the port)
  * While true, accept a connection, and create a thread to deal with the client - basic flow of logic in a server that supports multiple client connections
