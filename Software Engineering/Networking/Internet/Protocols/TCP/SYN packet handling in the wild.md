# [SYN packet handling in the wild](https://blog.cloudflare.com/syn-packet-handling-in-the-wild/)

## The tale of two queues

* Each bound socket in the LISTENING TCP state has two separate queues (SYN and Accept)

### SYN Queue

* Stores inbound SYN packets
* Response for sending out SYN+ACK packets and retrying them on timeout
* After transmitting the SYN+ACK, the SYN Queue waits for an ACK packet from the client - the last packet in the three-way handshake
* All received ACK packets must first be matched against the fully established connection table, and only then against data in the releveant SYN Queue
* If the data matches the SYN Queue, the kernel removes the item from the SYN Queue, creating a connection and adding the connection to the Accept Queue

### Accept Queue

* Contains fully established connections
* When a process calls `accept()`, the sockets are de-queued and passed to the application

### Queue size limits

* The SYN Queue cap is defined by the `backlog` parameter passed to the `listen` syscall

### Perfect backlog value

* Can also think of SYN Queue size as ACK packets in flight
  * The longer the average round trip time to the client, the more slots are going to be used
  * For many clients far away from the server, it makes sense to increase the backlog value
* Too large of a SYN Queue size may use too much memory
  * During a SYN Flood attack, it makes no sense to waste resources on storing attack packets

## Slow application

* When the Accept Queue gets full (size of `backlog` + 1)
 * Inbound SYN packets to the SYN Queue are dropped
 * Inbound ACK packets to the SYN Queue are dropped
 * The TcpExtListenOverflows counter is incremented
 * The TcpExtListenDrops counter is incremented
* Dropping inbound packets is a push-back mechanism - the other party will attempt to resend the SYN / ACK packets at some point, at which point the slow application has recovered (hopefully)

## SYN Flood

* SYN Cookies are a construct that allows the SYN+ACK to be generated statelessly without actually saving the inbound SYN and wasting system memory
* When the other party is real, it will respond with a valid ACK packet that includes the reflected sequence number
