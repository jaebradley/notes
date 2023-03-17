# [IP addresses & routing](https://jvns.ca/blog/2018/07/24/ip-addresses-routing/)

* There are 14 fields in the IP header, but the important ones are the source IP address, the destination IP address, and the TTL
* Note that the _port_ is not part of the IP header - the TCP and UDP protocols have ports, but that lives at a different network level

## What's a subnet?

* `168.23.0.0/8` CIDR notation means all the packets that have the same first 8 bits as the packet `168.23.0.0`
  * In this case, that would be `168.*.*.*` since each of the four numbers in an IP address is 8 bits

## When I create a packet on my computer, what happens to it?

*  Packet with IP address `1.2.3.4`
*  There is a route table, where each route has a subnet, a network device, potentially instructions on where to send packets (like a MAC address)

### destination NAT

* Can use iptables rules to force packets destined for ip address X to instead go somewhere else
* In the article example, the rule forces TCP packets that are going to a specific ip address and port to instead go to a different ip address and port
* There's a `--jump DNAT` configuration argument which indicates that Linux won't just rewrite packets that were supposed to go to ip address X to instead go to ip address Y, but also to modify reply packets from ip address Y to make them appear as if they came from ip address X
  * So from the perspective of the application that's talking to ip address X, it won't know that it's actually not talking to ip address X

### source NAT

* Rewrites source IP addresses
* Containers have IP addresses that are used to send packets to the outside world
* The outside world has no idea about these IP addresses that are specific to the container, and will not be able to reply to those packets, and pretend that they come from your host
