# Building Blocks of TCP

## Three-Way Handshake

* Before the client and server can exchange any application data, they must agree on starting packet sequence numbers
* Client picks some random sequence number and sends a `SYN` packet containing other TCP configuration information
* Server increments the random sequence number by one, picks its own random sequence number, appends its own set of flags / options and responds with a `SYN ACK` packet
* Client increments both its random sequence and the server random sequence by one and completes the handshake by returning an `ACK` packet
* Once the three-way handshake is complete, application data can begin to flow between the client and the server
* Each new TCP connection will have a full roundtrip of latency before any application data can be transferred
* TCP connection reuse is a critical optimization for any application running over TCP

## Flow Control

* Mechanism to prevent sender from overwhelming the receiver with data it may not be able to process
  * Receiver may not be willing to allocate a fixed amount of buffer space
* Each side of the TCP connection advertises its own receive window, which communicates the size of the available buffer space to hold the incoming data
* When the TCP connection is first established, both sides initiate their receive window values
* If client or server is not able to keep up with the data stream from its counterpart, it can advertise a smaller window to the sender
* If the window reaches zero, then it is treated as a signal that no more data should be sent until the existing data in the buffer has been cleared
* Each `ACK` packet carries the latest window value for each side, allowing both sides to dynamically adjust the data flow rate to the capacity and processing speed of the sender and receiver

## Slow Start

* Client located in NYC attempting to retrieve a file from a server in London
  * 3-way handshake, both parties advertise their respective receive window sizes
  * The only way to estimate the available capacity between the client and the server is to measure it by exchanging data
  * Server initializes a new congestion window variable per TCP connection and sets its initial value to a conservative system-specified value
    * This congestion window size is a sender-side limit on the amount of data the sender can have in flight before receiving an ACK from the client
  * The maximum amount of data in flight between the client and the server is the minimum of the receive window and the congestion window
* Start slow and grow the window size as packets are acknowledged
  * Start value is 10 segments
* Modern servers can send up to 10 network segments to the client, at which point it must stop and wait for an acknowledgement
  * For each ACK, two new packets can be sent 
  * When there is packet loss, the congestion window size decreases by some multiple
* Every TCP connection must go through the slow-start phase
* Slow-start is not as big of an issue for large, streaming downloads, as the client and server will arrive at their maximum window sizes after a few hundred milliseconds and continue to transmit at near maximum speeds
* For many HTTP connections, which are short and burty, it is not unusual for the data transfer to finish before the maximum window size is reached
  * Slow-start limits the available bandwidth throughput, which has an adverse effect on the performance of small transfers
* Slow-start restart resets the congestion window of a connection after it has been idel for a defined period of time
  * Network conditions may have changed while the connection has been idle, so to avoid congestion, the window is reset to a "safe" default
  * It's generally recommended to disable SSR on the server to help improve performance of long-lived HTTP conections

## Bandwidth-Delay Product

* Maximum amount of unacknowledged, in-flight data between the sender and receiver is defined as the minimum of the receive and congestion window sizes
  * The current receive windows are communicated in every ACK and the congestion windows are dynamically adjusted by the sender
* If the sender or receiver are frequently forced to stop and wait for ACKs for previous packets this creates gaps in the data flow, which would consequently limit the maximum throughput of the connection
* Window sizes should be big enough such that either side can continue sending data until an ACK arrives back from the client for an earlier packet

## Head-of-Line Blocking

* TCP provides the abstraction of a reliable network running over an unreliable channel
  * Basic packet error checking, in-order delivery, retransmission of lost packets, etc
* Every TCP packet carries a unique sequence number and the data must be passed to the receiver in-order
  * If one of the packets is lost en route to the receiver then all subsequent packets must be held in the receiver's TCP buffer until the lost packet is retransmitted and arrives at the receiver
  * Application sees a delivery delay when it tries to read data from the socket
  * This delay / latency which results in unpredictable latency variation in packet arrival times is known as jitter
