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
