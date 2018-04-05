# Part 1 - Clones

* Public servers of a scalable web service are hidden behind a load balancer.
  * LB evenly distributes load onto group / cluster of application server
  * If Steve interacts with service, he might be served request by server 2, then his second request by server 9, etc.
* This also means that all servers should contain exactly the same codebase and not store user-related data, like sessions or profile pictures, on local disc or memory
* Sessions should be stored in a centralized data store to be accessed by application servers
  * External DB or external cache like Redis.
  * External means that the data store does not reside on application servers, but hopefully near data center of application servers
* Now that all servers are identical, can create an image file of one of these servers to build an Amazon Machine Image (AMI) that will be used to spin up new servers
