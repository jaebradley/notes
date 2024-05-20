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

### Stateless serving layer

* Message data is stored separately from brokers
* Cluster can assign ownership of a topic to any broker in the cluster at any time
* Thus, brokers / serving layer are "stateless" since there is no information stored on the brokers themselves that is necessary to handle client requests

## Bundles

* All topics in a Pulsar cluster are assigned to a specific bundle
* Each bundle is assigned to a different broker
* Bundle assignment is determined by hashing the topic name
* This evenly distributes topics across all brokers

## Load Balancing

* When an existing bundle exceeds some preconfigured thresholds (due to changes in traffic patterns where a broker is serving several topics with heavy traffic) the buundle is split into two new bundles with one of them being offloaded to a new broker

## Load Shedding

* Broker offloads one or more bundles to a new broker
  * The selected bundles are left intact (i.e. no splitting occurs)
* Load balancing corrects the distribution of topics across bundles because one of the bundles receives much more traffic than others and we want to distribute the load evenly across all bundles
* Load shedding corrects the distribution of bundles across brokers based on the resources required to service them
  * Even though each broker can be assigned the same number of bundles, the message traffic handled by each broker could be dramatically different if the load is unbalanced across the bundles
* Scenario with 20 bundles handling 90% of the message traffic
  * If most of these bundles happen to be assigned to the same broker, the broker's resources (CPU, network, memory) could be exhausted
  * Offloading these bundles to another broker will help alleviate the problem
  * Splitting the bundle via load balancing would only shed ~50% of the message traffic
