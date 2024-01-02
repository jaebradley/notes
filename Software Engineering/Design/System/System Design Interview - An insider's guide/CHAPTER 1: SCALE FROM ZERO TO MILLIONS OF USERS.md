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
