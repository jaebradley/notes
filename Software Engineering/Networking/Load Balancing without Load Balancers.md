# [Load Balancing without Load Balancers](https://blog.cloudflare.com/cloudflares-architecture-eliminating-single-p)

* CloudFlare's data centers all have a router
  * Router determines the paths packets take from the Internet to CloudFlare's network
* 1+ switches that aggregate traffic within a data center's local area network
* Behind each switch is a set of servers
  * Servers perform tasks like DNS resolution, proxying, caching, logging
* Instead of relying on a piece of hardware to load balance across its network, CloudFlare uses routing protocols to spread traffic and handle failure

## Anycast Is Your Friend

* IP addresses generally correspond to a single device connected to the public Internet
* A home or office may have multiple devices sitting behind a gateway using network address translation
  * Only one public IP address
  * Devices that sit behind the network use a unique private IP address
  * 1 unique IP per set of devices (Unicast)
* Broadcast routing scheme is when one node sends packets that hit all recipient nodes
  * Not implemented in IPv6
* Multicast routing scheme is when one node sends packets that hit multiple (but not all) recipient nodes that have opted into a group
* Unicast and Anycast have a one-to-one routing scheme (1 sender, 1 receiver)
  * Unicast has only one possible destination
  * Anycast can have multiple possible destinations
  * Anycast network picks preferred route to destination
    * On a WAN like the Internet, preference is for shortest path
    * On a LAN, preferences can be set with weights

## Anycast at the WAN

* Every router in each of CloudFlare's data centers announces their external-facing IP addresses
* Packet sent to `173.245.58.205` passes through a series of routers
  * Routers look at the available paths to CloudFlare's end points and send the packet down the path with the fewest hops along the way
* For simple protocols like UDP that don't maintain state, Anycast is ideal and has been used widely to load balance DNS for some time
* Benefit of TCP over Anycast is that they can lose an entire data center and packets flow to the next closest facility without hiccup

## Anycast in the LAN

* Each server within CloudFlare's data centers is set up to receive traffic from any of CloudFlare's public IP addresses
* All servers announce a route across the LAN for all IPs
  * Each server assigns a weight to each IP route
  * Router configuration with route with lowest weight preferred
* If a server crashes, Bird routes traffic to server with next-lowest weighted route
* Monitor critical processes on each server
  * If critical process fails, then Signal for Bird to withdraw route
* Monitors server's load as well as load on other servers in the data center
  * If a particular server becomes overloaded and there is sufficient capacity elsewhere, routes can be withdrawn to take some traffic away from the overloaded server
