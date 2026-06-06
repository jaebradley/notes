# [Why gRPC Is Fast: The Real Reason Is HTTP/2, Not Just Protobuf](https://javarevisited.substack.com/p/why-grpc-is-fast-the-real-reason-818)
* TCP maintains a variable for each connection - the congestion window - used by the source (like the browser client) to limit how much data it is allowed to have in transit at a given time
* Whenver a timeout occurs in TCP, this timeout is treated as a sign of congestion, and the data transmission rate is reduced
* The congestion window is halved, and continuous to be halved after each timeout
* Every time the source successfully sends packets equivalent to the congestion window variable (each packet sent during the last round-trip time has been ACKed) a packet is added to the congestion window value
* The reason for the additive increase and the multiplicative decrease is that when the congestion window is too large, any packets that are dropped are retransmitted
* These retransmissions make congestion worse

## Slow Start
* Initially, the congestion window value is set to `1`
* After the packet is ACKed, the congestion window value is incremented and two packets are sent
* When both packets are subsequently ACKed, TCP increments the congestion window by `2` (one for each ACK and sends four packets
* So, initially the congestion window increase is exponential until packets are dropped
* Then the congestion window decrease is exponential
* Then the congestion window increase is additive / linear

## HTTP/2 - Multiplexed Streams
* Single TCP connection, but multiple “streams” can exist within that single TCP connection
* “Stream” is a bi-directional, independently flow-controlled virtual channel
* Since a single TCP connection is used, if some packets are dropped, they need to be re-transmitted
  * This menas that all other packets are blocked until successful re-transmission occurs
