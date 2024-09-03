# [The journey of an internet packet: Exploring networks with traceroute](https://sebastianmarines.com/post/journey-of-a-packet-exploring-networks-with-traceroute/)

## The life of a packet

* If destination IP address is in the same network as your computer, packet is sent to destination IP address
* It destination IP address is not in the same network, the packet is sent to the default gateway, which is your ISP's router that connects your network to the public internet
* Router checks to see if the destination IP address is in its routing table
  * If so, the router sends the packet directly to the destination
  * If it is not, the router sends the packet to the next router in the path, until the packet reaches its destination
* Computer with IP address of `10.0.0.1` pings computer with IP address `10.0.0.2` and sends an ICMP
  * Computers are not in the same network so the packet is sent to the router with an IP address of `10.1.0.5`
  * Router in that network forwards packet to the router with an IP address of `10.1.0.6`, which continues this forwarding pattern until the packet reaches the destination computer

## What happens when there is a problem?

* `traceroute` shows the path a packet takes from source to destination
* Displays every router in the path, and the response time for each router

## How does `traderoute` work?

* Relevant IP header fields are *source address*, *destination address*, and *TTL* fields
* TTL field is used to prevent packets from looping indefinitely in a network
