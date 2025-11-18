# [The Internet is Cool. Thank you, TCP](https://cefboud.com/posts/tcp-deep-dive-internals/)
* Common analogy: the IP address is the building and the port is the apartment
  * Processes "live" in the apartment since they bind to ports
* The machine receiving data over a network has a finite amount of space, and must store the incoming data somewhere before passing this data to an application
  * This temporary storage is the `receive buffer` and is managed by the kernel
* Since the receive buffer has finite space, the receiver needs to tell the sender how much more data it can handle
  * This mechanism is called flow control and TCP segments include a field called the window
  * The window specifies how much data the receiver is currently willing to accept
```
  0                   <----- 32 bits ------>                     
  0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |        Source Port              |     Destination Port        |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |                        Sequence Number                        |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |                    Acknowledgment Number                      |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 | Header|Rese-|   Flags   |       Window Size                   |
 | Len   |rved |           |                                     |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |       Checksum                  |     Urgent Pointer          |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |                    Options (if any)                           |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |                    Data (Payload)                             |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

* Each TCP segment is contained in an IP packet
* Each transport-layer connection is a tuple of 5 values (TCP/UDP, source IP, source port, destination IP, destination port)

## Sequence and Acknowledgment Numbers
* Sequence number indicates which bytes a segment carries
  * Let's the receiver detect and reorder out-of-order segments and identify package losses
* Acknowledgement number indicates which bytes have been received
* TCP uses cumulative acknowledgement
  * ACK of 100 means that bytes 0-99 were received
  * If bytes 100-120 are lost but bytes 120-140 arrive, the ACK remains 100 until the missing data is received
```
1. A --> B: Send [Seq=0-99]
2. B --> A: Send [Seq=0-49]

3. B --> A: Receives A's [0-99] --> sends ACK=100
4. A --> B: Receives B's [0-49] --> sends ACK=50

5. A --> B: Send [Seq=100-199]   --- lost ---
6. B --> A: Send [Seq=50-99]     --- lost ---

7. A --> B: Send [Seq=200-299]
   B receives --> notices gap (100-199 missing) --> sends ACK=100

8. B --> A: Send [Seq=100-149]
   A receives --> notices gap (50-99 missing) --> sends ACK=50

9. A --> B: Send [Seq=300-399]
   B still missing 100-199 --> sends ACK=100

10. B --> A: Send [Seq=150-199]
    A still missing 50-99 --> sends ACK=50

11. A --> B: Retransmit [Seq=100-199]
    B receives --> now has 0-399 --> sends ACK=400

12. B --> A: Retransmit [Seq=50-99]
    A receives --> now has 0-199 --> sends ACK=200
```

## TCP Flags
* `SYN` - used to establish a connection
* `ACK` - indicates the Acknowledgement number is valid
* `FIN` - indicates connection teardown
  * `A -> B`: `FIN`
  * `B -> A`: `ACK`
  * `B -> A`: `FIN`  (I want to disconnect too)
  * `B -> A`: `ACK`
* `RST` - reset flag that indicates error or forced shutdown (drop the connection immediately)
  * Operating system sends `RST` if no process is listening or if the listening process crashed

## Window
* `Recv-Q` : shows bytes received but not yet read by the application
* `Send-Q` indicates bytes not yet acknowledged by the remote host
* `rb{X}`  indicates the receive buffer size
* `tb{X}` indicates the transmit buffer size
  * Data waits in the transmit buffer before being sent over the network
