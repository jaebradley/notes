# [SYN flood attack](https://www.cloudflare.com/learning/ddos/syn-flood-ddos-attack/)

* By repeatedly sending initial connection request (SYN) packets, the attacker is able to overwhelm all available ports on a targeted server machine

## How does a SYN flood attack work?

* TCP connection has three distinct processes in order to make a connection
  * Client sends a SYN packet to the server to initiate the connection
  * Server responds to the initial packet with a SYN/ACK packet, acknowledging the communication
  * Client returns ACK packet to acknowledge receipt of the packet from the server
    * TCP connection is open and able to send / receive data
* Attacker sends many SYN packets
* Server responds to each connection request and has an open port ready to receive the response
* Server waits for final ACK packet (which never arrives)
  * Attacker continues to send more SYN packets until all available ports have been utilized on the server, rendering it unable to function normally

## How is a SYN flood attack mitigated?

* One mitigation strategy involves overwriting the oldest half-open connections once the backlog has been filled
  * Requires that legitimate connections can be fully established in less time than the backlog can be filled with malicious SYN packets
* Server responds to each connection request with a SYN-ACK packet but then drops the SYN request from the backlog, removing the request from memory and leaving the port open and ready to make a new connection
  * For legitimate requests, the client will send a final ACK packet, and the server reconstructs with some limiations) the original SYN packet
  * There is information loss about the TCP connection, but better handles denial-of-service attacks
