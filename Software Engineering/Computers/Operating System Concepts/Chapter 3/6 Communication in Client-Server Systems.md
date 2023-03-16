# 3.6 Communication in Client-Server Systems

## 3.6.1 Sockets

* A pair of processes communicating over a network employ a pair of sockets - one for each process
* A socket is identified by an IP address concatenated with a port number

```java
try {
  ServerSocket socket = new ServerSocket(6013);
  while (true) {
    Socket client = socket.accept();
    PrintWriter printWriter = new PrintWriter(client.getOutputStream(), true);
    printWriter.println(new Date().toString());
    client.close();
  }
} catch (IOException ioe) {
  System.err.println(ioe);
}
```

* Server begins listening to port `6013` via the `accept` method
  * Server blocks on the `accept` method until a client requests a connection
* Once a connection request is received, the server communicates with the client by using the `printer` object to write the date to the client
* Once the server has written the date, the server closes the socket to the client and resumes listening for more client requests
* Sockets allow only an unstructured stream of bytes to be exchanged between the communicating threads

## 3.6.2 Remote Procedure Calls

* Each RPC message is addressed to an RPC daemon listening to a port on the remote system, and each contains an identifier of the function to execute and the parameters to pass to that function
  * The function is then executed as requested, and any output is sent back to the requester in a separate message
* If a remote process needs a service, it addresses a message to the proper port
  * If a system wished to allow other systems to be able to list its current users, it would have a daemon supporting such an RPC attached to a port
  * Any remote system could obtain the needed information by sending an RPC message to the pre-determined port on the server
* There can be data representation differences between client and server - for example, the representation of 32-bit integers, which may be represented as big-endian (storing most significant bytes first) or little-endian (storing least significant byteds first)
  * External data representation is used to "marshall" the data passed in an RPC message in a machine-independent format (which will then be converted into a machine-dependent format)
* For "at most once" message execution, the server must keep a history of all the timestamps of messages it has processed / history to ensure repeated messages are detected
  * Incoming messages that already have a timestamp associated with the same message are ignored
  * Client can send same message multiple times and be assured they will get one response back, at most
* For "exactly once", need to remove risk that server will never receive request
  * Server must implement "at most once", but also send ACK messages to the client that the message was received and executed
  * Client resends RPC call periodically until it receives the ACK for the call
* Port binding can be done statically at compile time, where the ports are well-known / pre-determined
* Dynamic port binding is done via a "matchmaker" daemon on a fixed RPC port on the server side, that the client can introspect
  * Client sends name of the remote procedure call, and the rendezvous daemon sends back the port associated with that RPC

## 3.6.3 Pipes

### 3.6.3.1 Ordinary Pipes

* Allows two processes to communicate in producer / consumer fashion
  * The producer writes to one end of the pipe and the consumer reads from the other end (unidirectional)
  * The `pipe` method in Unix takes an array of file descriptors where the first file descriptor in the array is the rear-end of the pipe (consumer reading end) and the second file descriptor in the array is the write-end (producer-writing end)
  * Unix treats pipes as special types of files, thus pipes can be accessed using `read` and `write` system calls
* Pipes cannot be accessed from outside the process that creates it
  * Typically, a parent process creates a pipe and uses it to communicate with a child process it creates via `fork`
  * Child processes inherit open files from its parent
  * Since a pipe is a special type of file, the child inherits the pipe from the parent process

### 3.6.3.2 Named Pipes

* Named pipes can be directional, and several processes can use it for communication
* Named pipes continue to exist after communicating processes have finished (unlike ordinary pipes, where the ordinary pipe does not exist after the processes have finished communicating)
* Named pipes are referred to as FIFOs in UNIX systems and appear as typical files in the file system

