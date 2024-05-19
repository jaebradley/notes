# Chapter 2: Pulsar concepts and architecture

## Pulsar's physical architecture

* A Pulsar instance is one or more Pulsar clsuters that act together as a single unit
* Each instance has an instance-wide ZooKeeper cluster called the configuration store which retains information that pertains to multiple clusters (like geo-replication)

## Pulsar's layered architecture

* When a Pulsar client accesses a topic that has not been used yet
* Process is triggered to select the broker best-suited to acquire ownership of the topic
* Once a broker assumes ownership of a topic, it is responsible for handling all requests for that topic and any clients wishing to publish to or consume data from the topic need to interact with the corresponding broker that owns it
* Broker information is contained in ZooKeeper metadata and will change based on load rebalancing, etc.
* Cannot directly access brokers themselves, but instead communicate via the Pulsar proxy

## The Pulsar Proxy

* A "traditional" load balancer would route requests to a random broker
  * If a broker receives a request for a topic it isn't serving, it will automatically reroute the request to the appropriate broker
  * But this incurs a nontrivial penalty
* Proxy uses Pulsar's service discovery to determine which broker is hosting the topic you are trying to reach and route the client request to that broker
* Proxy will cache information in memory for future requests to streamline the lookup process
* Run more than one Pulsar proxy behind a traditional load balancer
  * Proxies can handle any (random) request so they can be "traditionally" load balanced without any issue
