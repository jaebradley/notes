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
