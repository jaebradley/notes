# 13.3.2 Network Devices

* Performance and addressing characteristics of network I/O differ from disk I/O
* Wall socket analogy
* Socket interface operations
  * Socket creation
  * Connect local socket to a remote address
  * Listen for any remote application to plug into the local socket
  * Send / receive packets over connection
* Socket interface `select` function returns information about a set of sockets
  * Which sockets have a packet waiting to be received
  * Which sockets have the room to accept a packet that was sent
