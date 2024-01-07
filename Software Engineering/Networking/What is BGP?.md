# [What is BGP?](https://www.cloudflare.com/learning/security/glossary/what-is-bgp/)

* Border Gateway Protocol is the postal service of the Internet
* Someone puts a letter in the mailbox, the Postal Service processes that piece of mail
  * Postal Service chooses a fast, efficient route to deliver that letter
* When somebody submits data via the Internet, BGP is responsible for looking at all the available paths that the data could travel and then picking the best route

## What is an autonomous system?

* Internet is a network of hundreds of thousands of smaller networks
* Smaller networks are known as autonomous systems
* Each autonomous system is essentially a large pool of routers run by a single organization
* In Postal Service analogy, autonomous systems are like individual post office branches
  * Town has many mailboxes
  * Mail for all the mailboxes goes through local post office before being routed to their destination
  * Internal routers within a given autonomous system are like mailboxes
    * Internal routers forward their outbound transmissions to the autonomous system
    * Autonomous system uses BGP routing to deliver outbound transmissions to their destination
* Because the topology of the Internet is constantly changing, every autonomous system must have up-to-date information regarding new and obsolete routes
* Each AS has a peering session where it connects to neighboring ASes with a TCP/IP connection to share routing information
* ASes can be part of different organizations / competing businesses
  * BGP routes often times take business considerations into account
  * ASes often charge each other to carry traffic across their network

## External vs. Internal BGP

* External BGP is like international shipping
  * Each country may have different standards when shipping a piece of mail internationally
* When a piece of mail reaches its destination country, it has to go through the destination country's local mail service
  * Each country may have its own internal mail service with different guidelines and rules than other countries
  * Each AS can have its own internal routing protocol for routing data within its own network

## BGP flaws and how to address them

* Turkish ISP (TTNet) accidentally advertised incorrect BGP routes to their neighbors
* Routes claimed that TTNet was the best destination for traffic on the Internet
  * As routes spread to more and more autonomous systems, disruption occurred where many people could not access some / all of the Internet
* Pakistani ISP attempted to use a BGP route to block Pakistani users from visiting YouTube
  * ISP accidentally advertised routes to neighboring ASes
  * Routes spread across Internet's BGP network
  * Route sent users attempting to access YouTube to a dead end
  * Made YouTube inaccessible for several hours
