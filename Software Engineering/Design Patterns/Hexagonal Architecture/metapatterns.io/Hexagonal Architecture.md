# [Hexagonal Architecture](https://metapatterns.io/implementation-metapatterns/hexagonal-architecture/#ports-and-adapters-hexagonal-architecture)

## Dependencies
* Adapters bridge the gap between the the core that contains business logic, and an adapted component
* Adapters should be small - they are dependent on the interfaces of the adapter and the core
* Leaky abstraction is an interface that looks generic but it actually has a contract that matches that of the component it encapsulates
  * This complicates changing the internal component for some other vendor
* Defining a Service Provider Interface in terms of your service's business logic and writing an adapter between the SPI and the vendor prevents vendor lock-in
  * Can change the external provider by writing another adapter to a different vendor

## DDD-Style Hexagonal Architecture, Onion Architecture, Clean Architecture
* Isolate the domain layer from the database - application asks the database adapter (commonly called the repository) to create domain objects (aggregates)
* Application then executes business actions on the aggregates, and tells the repository to save any modified aggregates to the database

## Cell, Cluster, Domain
* A "cell" is an encapsulated cluster of services which implement a subdomain and are usually deployed as a single unit
* Generally, the communication inside a cell is synchronous, allowing for complex orchestrated use cases
* Communication _between_ cells are asynchronous and cells are loosely coupled
* Ambassador plugins run pieces of business logic that belong to other cells inside a host cell
  * This is primarily done to avoid slow intercell communication
