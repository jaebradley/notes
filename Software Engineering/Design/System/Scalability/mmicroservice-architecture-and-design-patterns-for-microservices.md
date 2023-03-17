# [Microservice Architecture and Design Patterns for Microservices](https://medium.com/@madhukaudantha/microservice-architecture-and-design-patterns-for-microservices-e0e5013fd58a)

## Decomposition

* Principle is to make services loosely coupled (single responsibility principle)
* Can decouple by business capability like "Order Management" or "Customer Management"
  * Can further decouple by core, supporting, and generic services
  * Order management can be decoupled into a product catalog service or an inventory service
* Strangler pattern
  * Parallelize new service with existing application / monolith
  * Slowly route traffic to new service
  * Remove old functionality from existing application / monolith

## Integration

* API Gatewoy
  * Can help be the single entry point for any microservice call
  * Can work as proxy and route request to the appropriate microservice
    * Might have a routing map of an HTTP method and path to the relevant service URL
  * Can aggregate results to send back to consumer
  * Can handle authorization and authentication
* When aggregating data from many microservices, can use gateway or a composite microservice that is responsible for calling all required microservices, stitching together the data and sending it back to consumer

## Database Patterns

* Microservices requirements
  * Loosely-coupled services that can be developed, deployed, and scaled independently
  * Business transactions that may span multiple services
  * Business transactions that need to query data owned by multiple services
  * Different services have different data storage requirements
* There should be one database per microservice
  * Only accessible by microservice API
* Command Query Responsibility Segregation - Create / Update / Delete is handled by the "command" side of application while "query" side handles querying using materialized views
  * Generally used with event-sourcing pattern where materialized views are kept updated by subscribing to stream of events

### Event Sourcing

* Append-only data store that stores sequences of events
* Each event is described as an "action" like `AddItemToOrder`
* Event store acts as system of record
* Events that are published by event store are subscribed to by materialized views
* These materialized views are then used by UI and when new events are added, materialized views are updated, which then leads to updated UI


### Saga Patterns

* Choreography
  * No central coordinator
  * Each service produces events and listens to other service's events
  * Best used when coordinating across domains of control
* Orchestration
  * Single object is responsible for sequencing of business logic
  * One domain of control

## Observability Patterns

* Centralized logging service that aggregates logs from each service instance
* Distributed Tracing - request span multiple services making it hard to trace a request end-to-end
  * External request gets unique external request id
  * Pass external request id to all services
  * External request id is logged in all log messages
* Healthcheck - each service needs to have an endpoint that can be used to check health of application

## Service Discovery

* When containers are spun up, IP addresses are assigned dynamically
* If a consumer depends on exact IP addresses, then it can break
* Instead, create a service registry which keeps metadata of each producer service and the specification for each
* When a service instance is spun up it should register with registry and obviously de-register when shutting down

## Circuit Breaker Pattern

* When a service calls another, there is a chance that downstream service is down
* Requests will keep going to down service, using up resources
* Consumer invokes downstream service via proxy that keeps track of service failures
* When failures pass some limit, all attempts to call downstream service will be failed immediately for some time period
* After timeout period expires, circuit breaker allows a limited number of requests to pass through
* If requests succeed, then service resumes normal operation
