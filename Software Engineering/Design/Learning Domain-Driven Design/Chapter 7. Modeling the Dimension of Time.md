# Chapter 7. Modeling the Dimension of Time

* Example of a CMS that tracks leads and related information like their name, status, phone number, etc
* While the table data documents the leads' current states, it misses the story of how each lead got to its current state
  * Don't know how many calls were made before lead become `CONVERTED`
  * These questions around the lead lifecycle reflect business concerns that are essential for optimizing the sales process
* Event sourcing pattern introduces the dimension of time into the data model
* Persists events documenting every change in an aggregate's lifecycle
* Example of these events for a lead would be
  * an event type of `lead-initialized` with all the lead details at the time (name, phone number) and the time of the event
    * This event would have a lead id and an event id
  * Next event is a `contacted` event type, with the same lead id but an incremented event id
  * Follow-up event, contact details updated event, contacted (again) event, order-submitted event, payment-confirmed event (with an updated `status` of `converted`) follow
* Customer state can be "calculated" from these domain events by applying the transformation logic sequentially for each event
  * This also applies to any "subset" of events in an aggregate's timeline - so we can calculate what an entity's state is at any point in its lifecycle

## Source of Truth

* All changes to an object's state should be represented and persisted as events
* The database (the event store) that stores the system's events is the only strongly consistent store as it is the sytem's source of truth

## Event Store

* Should not allow modification or deletion of events - it is append-only
* The `expectedVersion` when adding events is needed to implement optimistic concurrency management
  * When adding new events, the version of the entity that the event is following needs to be specified
  * If the version of the specified entity is stale, and new events were added after the expected version, then the event store should raise a concurrency exception

## Event-Sourced Domain Model

* Each operation of an event-sourced aggregate does the following
  * Load the aggregate's domain events
  * Reconstitute the aggregate's state by applying the events into a state representation that can be used to make business decisions
  * Execute the aggregate's command to execute the business logic, and then produce new domain events
  * Commit the new domain events to the event store

### Advantages

* Time travelling - where domain events can be used to "reconsitute" an aggregate's current state and be used to restore all past states of the aggregate
  * This can help lead to debugging - can revert an aggregate to the exact state it was in when a bug was observed
* Audit log - the domain events represet a strongly consistent audit log of everything that has happened to the aggregates' states

### Disadvantages

* Evolving the model - events are immmutable, so adjusting an event's schema is quite complex

## Performance

* Performance hit will only be noticeable after 10k+ events per aggregate
  * Most sytems won't have an aggregate have more than 100 events
* To tackle any performance issues the snapshot pattern can be implemented
  * Process continuously iterates new events in the event store, generating corresponding projections, storing them in a cache
  * In-memory "projection" / aggregate "representation" is needed to execute an aggregate action
  * Process fetches the current state representation from the cache
  * Process fetches the events that came after the snapshot version from the event store
  * Process applies these additional events (in-memory) to the "snapshotted" aggregate

## Deleting Data

* Store all sensitive data in encrypted form, at rest
* Encryption key is stored in some key storage
* Encryption key is tied to an aggregate's ID
* When sensitive data needs to be deleted, the encryption key is deleted and thus the sensitive data is no longer readable

## Why can't I just write logs to a text file and use it as an audit log?

* Reliant on two transactions
* The database and the text file
* If the first transaction fails, the second transaction needs to be rolled back

