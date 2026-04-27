# CHAPTER 1: SCALE FROM ZERO TO MILLIONS OF USERS

## Database Choices

* Non-relational databases might be the right choice when application needs
  * Very low-latency
  * Data is unstructured / do not have any relational data
  * Only need to serialize and deserialize data
  * Large amount of data stored

## Load Balancer

* Evenly distributes traffic amongst web servers
* Users connect to the public IP address of the load balancer
* Web servers are no longer directly reachable via the public internet
  * Private IP addresses are used to communicate between servers

## Database Replication

* Leader / follower setup
  * Leader supports write operations
  * Follower database copies data from the leader
  * Follower nodes only support read operations
* If leader database goes offline, a follower database will be promoted to leader
* Promoting a new leader is complicated as the data in the follower database may not be up-to-date
  * Missing data needs to be updated by running data recovery scripts
  * Can also use multi-leader and circular replication

## Content delivery network (CDN)

* Network of geographically dispersed servers used to deliver static content
* Akamai example
  * Origin image URL: `https://foo.com/bar.jpg`
  * CDN / Akamai image URL: `https://foo.akamai.com/bar.jpg`
  * If CDN server does not have the cached image, it requests the image from the origin server
    * Origin server returns the image to the CDN server, with a TTL header
  * CDN caches image, and image remains in CDN until the TTL expiration
* If there is a temporary CDN outage, clients should be able to detect the problem and request resources from the origin
* CDN file invalidation (before expiration) can be done via CDN provider API or by using object versioning to generate a different CDN URL (for example, by adding a `version` query parameter)

## Stateless web tier

* Stateful web servers store session data and remember client data from one request to the next
  * One downside is that every request from the same client must be routed to the same server
  * Can be done with sticky sessions in a load balancer
  * Adding / removing servers becomes trickier
* Stateless web servers move session data to persistent storage (database)
  * Each web server in a cluster can access state data from these databases

## Message queue

* Durable component, stored in memory, supporting asynchronous communication
* Servers as a buffer and distributes asynchronous requests
* Input services produce / publish messages to a queue
* Consumer / subscriber services connect to these messages queues and use the message details to peform some action
* Message queues allow decoupling of producers to process requests even when the consumer is unavailable to process them
  * Conversely, consumers can process messages even when producers are unavailable

## Database Sharding

* Sharding key needs to distribute data evenly
* Resharding data when a single shard holds more data due to growth
  * Can be challenging as this involves updating the sharding function and moving data around (consistent hashing is used to tackle this challenge)
* Hotspotting can still occur when a shard contains many popular objects
  * Solution is to reshard
* Difficult to join across multiple servers
