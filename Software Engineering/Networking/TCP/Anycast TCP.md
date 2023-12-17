# [Anycast TCP](https://bill.herrin.us/network/anycasttcp.html)

## Routing Types

* Unicast: send packet to IP address, routing to single computer configured for that IP address
* Broadcast: send packet to broadcast IP address, all computers within the broadcast domain receive the packet
* Multicast: send packet to multicast IP address, all computers subscribed to the multicast channel receive the packet
* Anycast: send packet to anycast IP address, closest computer in the (global) network receives the packet (and only that one computer)

## Problem Components

* Packets which have reached the wrong Anycast node are redirected to the correct Anycast node
* A VPN can be used to redirect packets that have reached the wrong Anycast node to be redirected to the correct Anycast node

## Anycast TCP Architecture

### TCP Reset

* Usually, computer sends a TCP RST packet when a connection has been attempted (TCP SYN) to a TCP port, or when a non-SYN packet arrives which does not correspond to any connection within the local computer's TCP state table
* TCP Reset hook modification is to prevent the TCP RST from being sent in the latter case
  * Received packet is forwarded to user-space program that determines which of the other anycast nodes owns the connection
  * Forwards the packet to that node via a tunneling protocol

### TCP Sequence Number Start Range

* Computer accepting a TCP connection ordinarily randomizes its starting sequence number across 32-bit space
* Each node in the anycast cluster is assigned a non-overlapping range of sequence numbers
* When the user-space program receives an unknown TCP packet, it looks up the node that is assigned to the sequence / ACK number for the packet
* TCP sequence numbers increment with the number of sent bytes
 * Connection sequence numbers should stay in the home range for as long as possible
 * Modify TCP stack and potential starting range of sequence numbers
  * Randomly select a starting sequence number within the home range that will still allow the node to transmit hundreds of MB on a connection before the connection's sequence number leaves the home range

### TCP Sequence Number High Water Mark

* Can't force the TCP sequence number to "wrap" and stay in a node's home range
* Before a connection climbs out of the home range, the other anycast nodes must be notified of the specific TCP connection ID so that any packets they receive can be rerouted to the node which owns the connection
 * TCP connection ID is the combination of the source and destination IP addresses and the source and destination TCP ports
* When transmission of a TCP packet brings the sequence number across the high water mark, user-space program notifies other anycast nodes that the current node owns the TCP connection with the given connection ID
 * The current node will remain the connection until the connetion closes, no matter the sequence number

### Connection close

* When the high water mark sequence number is surpassed for a connection, stateful information is transferred to the other anycast nodes (to tell the nodes that the TCP connection ID is owned by the current node)
* When the same connection is closed, these other anycast nodes must delete any related stateful information
