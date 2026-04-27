# [Introduction to Modern Network Load Balancing and Proxying](https://blog.envoyproxy.io/introduction-to-modern-network-load-balancing-and-proxying-a57f6ff80236)

## Overview

* Some clients are requesting resources from some number of backend servers
* Load balancer sits in-between the two to
  * Check which backends are available (and what addresses they have)
  * Check which backends are currently healthy
  * The algorithm to balance requests across healthy backends
* Benefits
  * Naming abstraction
    * Client just needs to know about the load balancer instead of keeping the addresses of all backend services
  * Fault tolerance
    * Can route around bad or overloaded backend

## Intro to L4 (Connection/Session) Load Balancing

* Client makes a TCP connection to load balancer
* Load balancer terminates connection, selects backend service, and makes new TCP connection with backend
* Load balancer roughly shuffles bytes back and forth and makes sure that bytes from same session wind up at the same backend service
* Shortcoming
  * HTTP2 clients talk to backend service
  * L4 load balancer makes single outgoing TCP connection for each incoming TCP connection (two incoming and two outgoing connections)
  * These connections are `kept-alive` (i.e. the connection is not closed when there are no active requests)
  * Client A sends 1 request per minute
  * Client B sends 50 requests per second over connection
  * So then backend selected to handle Client A will be handling 3000x less load than backend selected to handle Client B
    * This happens for any `multiplexing` (concurrent application requests over single L4 connection), `kept-alive` protocol
  * This defeats the purpose of load balancing

## Intro to L7 (Application) Load Balancing

* L7 load balancers solve previous problem by creating many different connections between load balancer and backend service for each HTTP/2 TCP connection between client and load balancer
* When client sends two HTTP/2 streams to load balancer, steam 1 is sent to backend 1 and stream 2 is sent to backend 2
* Multiplexing clients with different request loads will be balanced efficiently across backends

## Health Checking

* Two categories
  * Active - load balancer pings backend service (could be HTTP request to `/healtcheck` API)
  * Passive - load balancer detects health status based on data flow (could be some number of connection errors occurring)

## Security

* Load balancers often do rate limiting, authentication, DoS mitigation (IP address tagging, for example)

## Topologies

* Middle proxy
  * Many clients connect to single load balancer which connects to many backend services
  * Connect via DNS
  * Downsides
    * Load balancer is single point of failure
* Embedded Client Library
  * To avoid single point of failure, embed load balancer into client via a library
  * Downsides
    * Maintaining networking library across many different languages
    * Deploying library upgrades across large service architectures
      * Leads to potentially many different versions of library running in production
* Sidecar Proxy TODO:@jaebradley

## TCP/UDP Passthrough Load Balancers

* TCP connection is not terminated by load balancer
* Packets are forwarded to selected backend after connection tracking and Network Address Translation
  * Connection tracking - process of keeping track of the state of all active TCP connections
    * handshake completed
    * how long connection has been idel
    * backend selected for connection
  * NAT - uses connection tracking data to alter IP/port information of packets as they traverse load balancer
* Client talks to 1.2.3.4:80 and backend service is 10.0.0.2:9000
  * TCP packets arrive at load balancer at 1.2.3.4:80
  * Load balancer swaps destination IP and port of packet with 10.0.0.2:9000 and swaps source IP of packet with IP address of load balancer
  * Thus, when backend service responds to TCP connection, packets go back to load balancer, where connection tracking takes place and NAT can happen in reverse direction
* Benefits
  * Not terminating TCP connections means they do not need to buffer any TCP connection
  * Amount of state stored per connection is small and can be accessed via hash tables
  * Can handle substantially more active connections and packets per second than a terminating load balancer

## Direct Server Return

* DSR builds on passthrough
* Client requests to load balancer which passes on request to backend which returns response directly to client (instead of passing response back to load balancer and then onto client)

