# Background

* Stateless services are services which aggregate responses from other services
* Stateful services are services that generate a response by executing business logic on its state stored in some type of persistent store
* Many services only store and retrieve data by primary key and do not require the complex querying and management functionality offered by an RDBMS

## System Assumptions and Requirements

* Simple read / write operations to a data item that is uniquelly identified by a key
* State stored as a binary object
* No operations span multiple data items - there is no need for relational schema
* Objects are < 1MB in size
* Data stores that provide ACID guarantees tend to have poor availability
* Dynamo targets applications that operate with weaker consistency but higher availability
* Dynamo does not provide any isolation guarantees

## SLAs

* At Amazon, we have found that metrics expressed in average, median, and expected variance are not good enough if the goal is to build a system where all customers have a good experience rather than just the majority
  * If extensive personalization techniques are used, then customers with longer histories require more processing which impacts performance at the high-end of the distributed
  * An SLA in terms of mean / median response times will not address the performance of this important customer segment

## Design Considerations

* Data replication algorithms traditionally perform synchronous replica coordination in order to provide a strongly consistent data access interface
* To achieve this level of consistency, these algorithms are forced to tradeoff the availability of the data under certain failure scenarios
* An example of this is that rather than dealing with the uncertainty of the correctness of an answer, the data is made unavailable until it is absolutely certain that it is correct
* Optimistic replication techniques allow changes to propagate to replicas in the background, and concurrent, disconnected work is tolerated
* Conflicting changes must be detected and resolved (so not only when to resolve these changes, but "who" resolves the changes)
* Dynamo is an eventually consistent data store meaning that all updates reach all replicas eventually
* When to perform conflict updates (for example, during reads or writes)
  * Many traditional data stores execute conflict resolution during writes and keep the read complexity simple
    * In such systems, writes may be rejected if the data store cannot reach all / majority of replicas
  * Dynamo has a goal of being an always writeable data store (i.e. highly available for writes)
    * For a number of Amazon services, rejecting customer updates could result in a poor customer experience
    * For example, the shopping cart service must allow customers to add and remove items from their shopping carts even amidst network and server failures
    * Thus, conflict resolution must be a responsibility of reads in order to ensure that writes are never rejected
* Who performs conflict resolution (for example, last write wins)
  * Either the data store or the application can do conflict resolution
  * Data store must have simple policies (again, "last write wins")
  * Applications are aware of the data schema and can decide on a conflict resolution method that is best suited for its client's experience
    * For instance, the shopping cart application can choose to merge conflicting versions and return a single unified shopping cart
    * Default policy can still be "last write wins"
* Dynamo favors decentralized peer-to-peer techniques over centralized control due to outages occurring in the past for centralized systems


