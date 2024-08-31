# Chapter 9. Communication Patterns

## Integrating Aggregates

* Publishing the domain event from the aggregate may lead to the event being dispatched before the aggregate's new state is committed to the database
  * A subscriber may receive a notification, but it may contradict the aggregate's state (for example, in the case the commit took a while to execute)
* If the database transaction fails to commit and rolls back, the event has already been published and subscribers can read it
* Even if publishing domain events occurs _after_ database commit, this still can run into issues where the server fails right after committing the database transaction, but before publishing the domain events

## Outbox

* Both the updated aggregate's state and the new domain events are committed in the same atomic transaction
* Message relay fetches newly committed domain events from the database
* Relay publishes the domain events to the message bus
* After successful publish, relay marks the events as published or deletes them completely
* Use relational databases ability to commit to two tables atomically, and use a dedicated table for message storage
* For `NoSQL` databases that don't support multidocument transactions, embed the outgoing domain events in the aggregate's record
* Some relational databases allow tailing for updated / inserted records in the database's transaction log
* Some `NoSQL` databases expose committed changes as streams of events
* Outbox pattern guarantees at least once delivery of messages
  * If the message relay service fails right after publishing a message, but before marking it as published in the database, the same message will be published again in the next iteration

## Saga

* Core aggregate design principle is to limit each transaction to a single instance of an aggregate
* Cases where a business process spans multiple aggregates
  * Example: advertising campaign is activated, campaign publisher confirms advertising materials, campaign's state is changed to `Published`
	* If campaign is rejected by publisher, the campaign's state is changed to `Rejected`
* Sagas are business processes that span multiple transactions
* Sagas listen to events emitted by components and issue subsequent commands to other components
* If one of the execution steps fail, the saga is in charge of issuing relevant compensating actions to ensure the system state remains consistent
* Only data within an aggregate's boundaries are considered strongly consistent. Everything outside an aggregate's boundaries is eventually consistent
* Sagas match events to corresponding commands

## Process Manager

* If a saga contains `if-else` statements to choose the correct course of action, it is probably a process manager
* Example: booking a business trip starts with routing algorithm to choose most cost-effective route
  * Employee approval requested
  * If employee prefers a different route, manager approval requested
  * After flight is booked, one of the preapproved hotels has to be booked
  * If no hotels are available, the flight tickets are canceled
* A process manager is really just a decision tree organizer
  * Process manager simply initializes a process ("book a business trip")
  * And then walks through different decisions / consequences ("request employee approval", "request manager approval of employee override", etc)
