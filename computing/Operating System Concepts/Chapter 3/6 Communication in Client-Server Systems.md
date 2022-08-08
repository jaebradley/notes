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
