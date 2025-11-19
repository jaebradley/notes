# [How TCP Sockets Work](https://eklitzke.org/how-tcp-sockets-work)

## How Established Connections Work
* Each TCP file descriptor maps to a struct that has TCP-specific info like sequence numbers, current window size, receive buffer, write buffer
* When a new data packet comes in on the network interface card, the kernel is notified by being interrupted by the network interface card or by polling the network interface card for data
  * When the network interface card is busy, it is more efficient for the knerel to poll
  * If the network interface card is not busy, CPU cycles can be saved by using interrupts
* When the kernel receives a packet from the network interface card, it decodes the packet, identifying the TCP connection the packet is associated with based on the source IP, source port, destination IP, and destination port
* source + destination IP & port data is used to identify the in-memory `sock` `struct` associated with the connection
* If the packet is in sequence, then the data payload is copied into the socket's receive buffer
* Kernel will wake up any processes doing a blocking `read` or are `select`ing or `epoll_wait`ing on the socket
* When the userspace process calls `read` on the file descriptor, the kernel removes the data from the receive buffer and copies that data into a buffer supplied to the `read` system call
* When the userspace application calls `write`, it copies data from the user-supplied buffer into the kernel write queue
* Kernel copies data from the write queue into the network card interface
* Actual transmission of data to the network card interface could be delayed from when the user calls `write` (busy network, TCP send window is full)
* Kernel receive and write queues can fill up if the application is reading too slowly or writing too quickly
  * Kernel sets a maximum size for the queues, thus the maximum amount of kernel memory that each TCP socket could use would be the sum of the receive and write queue max sizes

## Read Semantics
* If the receive buffer is empty and the user calls `read` , the system call will block until data is available
* If the receive buffer is non-empty and the user calls `read` , the system call will immediately return with whatever data is available
  * Partial read occurs if the amount of data ready in the receive buffer is less than the size of the user-supplied buffer
* If the receive buffer is full and the other end of the TCP connection tries to send additional data, the kernel will refuse to `ACK` the packets (TCP congestion control)

## Write Semantics
* If the write queue is not full and the user calls `write`, the system call will succeed
* All of the data will be copied if the write queue has sufficient space
* If the write queue only has space for some of the data, then a partial write will happen and only some of the data will be copied to the buffer
* If the write queue is full and the user calls `write`, the system call will block

## How Newly Established Connections Work
* From the userspace point of view, newly established TCP connections are created by calling `accept` on a `listen` socket (designated via the `listen` system call)
* The `listen` system call takes a `backlog` argument
  * This argument controls how much memory the kernel reserves for new connections when the user isn't calling `accept` fast enough
* Scenario is a single-threaded HTTP server
  * Each request takes `~100ms` 
  * So the time between calls to `accept` will be `~100ms` 
  * At a high enough RPS, the kernel will need to make a choice
* Kernel can choose not to accept connections by refusing to `ACK` incoming `SYN` packet
  * More commonly what will happen is that the kernel will complete the TCP three-way handshake and then terminate the connection with TCP reset
* Alternative is that the kernel can accept the connection and allocate a socket structure (including receive and write buffers) and queue the socket object for later use
  * The next time the user calls `accept` , instead of blocking, the system call will immediately retrieve the already-allocated socket
  * More forgiving when the processing or connection rate is bursty - imagine 10 new connection requests coming in all at once
  * Excessive queueing can cause a lot of kernel memory to be allocated
  * Queueing can make the application appear slow to the other side of the connection (the client)
  * From the client's point of view, the client can establish new TCP connections, but when it tries to use the connection, the server is slow to respond
    * Argument that it's better to fail in this scenario so that there's more obvious feedback that the server is not healthy
    * If the server is aggressively failing connections, the client can also back off attempting to make connections as a form of congestion control

## Listen Queues & Overflows
* Kernel combines these approaches by queueing new connections, but a certain number of them - the number set by the `backlog` argument to the `listen` system call
  * `SOMAXCONN` is set to `128` and before kernel `2.4.2` this was the maximum value allowed
* When the listen queue fills up, new connections are rejected (listen queue overflow)
  * Read `/proc/net/netstat`  and check the value of  `ListenOverflows`  (global counter for the whole kernel)
