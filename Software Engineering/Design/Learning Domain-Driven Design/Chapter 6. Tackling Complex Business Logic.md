# Chapter 6. Tackling Complex Business Logic

## Domain Model

* Domain model pattern is intended to cope with cases of complex business logic
* Instead of CRUD interfaces, domain model pattern deals with complicated state transitions, business rules, and invariants - rules that have to be protected at all times
* Example of a help desk system with requirements like
  * Customers opening support tickets describing issues
  * Customers and support agents communicating via messages
  * Ticket escalation to agent's manager
  * Ticket's are automatically closed if the customer doesn't reply within 7 days
* Requirements are not simply a CRUD data entry screen and have complex lifecycle management logic

### Implementation

* Domain model is an object model incorporating both behavior and data
* Domain-driven design has tactical patterns, like aggregates, value objects, domain events, and domain services that are the building blocks of this object model

#### Complexity

* Model should be devoid of any infrastructural or technological concerns (implementing calls to databases, external system components)
* Model's objects are just _plain old objects_ and should implement business logic without relying on or directly incorporating any infrastructural components or frameworks
* Domain model's objects should follow the terminology of the bounded context's ubuiqtuitous language
* Domain model's objects should follow the domain experts' mental models

### Building Blocks

* Example `Color` value object made up of a `red`, `green`, and `blue` parameters
* Since changing one of the parameters would lead to a new color, and no two different colors can have the same values, and two instances of the same color must have the same values, no explicit identification field is needed to identify the colors
* Introducing an explicit `ColorId` field would lead to a potential bug, where two identical colors inserted into a database might have the same `red`, `green`, and `blue` values but different `ColorId` (i.e. row ID) values

#### Ubiquitous language

* Example class with `string` field definitions for things like phone numbers, country code, email address, etc
* This class would have to validate all these input fields
  * This leads to duplicative validation logic
  * Hard to enforce calling the validation logic before the values are used
* Instead, using actual value objects (like `PhoneNumber`, `EmailAddress` and `CountryCode` value objects) leads to increased clarity to communicating the intent of the stored values
* No need to call a field `countryCode` if the field type is `CountryCode` - can simply refer to the field as `country` as the type of the underlying value object makes the intent clear
  * value objects express the business domain's concepts and make the code speak the "ubiquitous language"
* No need to validate the values before assigned, as the validation logic resides in the value objects themselves
* A `Height` object (vs. simply using an `int`-based value) makes the intent of the object / field clear
  * It also decouples the measurement for a specific measurement unit
  * Can imagine that the `Height` value object is initializable using both metric and imperial units
  * And makes it easy to convert from one unit to another
* `PhoneNumber` value object could encapsulate logic to parse phone number from a string value, validate it, extract different attributes (like the country code)
  * In other words, the value object encapsulates the business logic that manipulates the underlying data / produces new instances of the value object

#### Implementation

* Value objects are implemented as immutable objects
  * Changing one of the value object's fields conceptually creates a different value - a different instance of a value object

#### When to use value objects

* Useful rule of thumbs - use value objects for the domain's elements that describe properties of other objects
  * Specifically, use value objects for properties of entities

### Entities

* An entity is the opposite of a value object
* It requires an explicit identification field to distinguish between the different instances of the entity
* Trivial starting example was a "person" with a single field - a name
  * Flaw is that different "people" can have the same exact name
  * Thus, an ID field is necessary to properly identify people
* ID field needs to be unique for each entity instance
* ID field value should remain immutable throughout the entity's lifecycle
* Entities are not immutable and are expected to change (unlike value objects)
* Value objects are used to describe / compose an entity's properties
* So the `Person` entity has two value objects - a `PersonID` and a `Name`

### Aggregates

#### Consistency enforcement

* Aggregate pattern defines a clear boundary between the aggregate and anything outside the aggregate
* Aggregate's logic validates all incoming modifications and ensures that these modifications do not contradict the aggregate's business rules / put the aggregate in an invalid state
* This consistency is enforced by only allowing the aggregate's internal business logic to modify the aggregate's state
* Processes / objects external to the aggregate are only allowed to read the aggregate's state
* Aggregate's state can only be mutated by executing mutating methods that are part of the aggregate's public interface
  * These mutation methods are often referred to as "commands" (like an `AddMessage` method)
  * Mutations can also be represented as a parameter object that encapsulates all the input values required to execute a command
    * So an `AddMessage` parameter object that has all the fields that represent adding a message
* Aggregate's public interface is responsible for validating all inputs and enforcing all relevant business rules and invariants
* All business logic related to the aggregate should be implemented in the aggregate
* This simplifies application layer that orchestrates operations on aggregates
  * Application layer loads the aggregate's current state
  * Executes the relevant aggregate action
  * Persists any modified state
  * Returns the operation's result to the application's caller

```java
public ExecutionResult Escalate(TicketId id, EscalationReason reason) {
  try {
    // Ticket Entity
    ticket = _ticketRepository.Load(id);
    command = new Escalation(reason);
    // Aggregate command execution
    ticket.Execute(command);
    // Save results to DB
    _ticketRepository.Save(ticket);
    return ExecutionResult.Success();
  } catch (ConcurrencyException ex) {
    return ExecutionResult.Error(ex);
  }
}
```

* DB used for storing aggregates needs to support concurrency management
* Simplest implementation is to have the aggregate hold a version field that is incremented after each update
	* Use optimistic concurrency control to ensure that the version value for an aggregate is / was the version that was originally read
  * i.e. increment the version counter of the aggregate in the database if the current aggregate value equals the aggregate version value that was read prior to attempting to apply changes to the aggregate's state

#### Transaction boundary

* An aggregate's state can only be modified by its own business logic
* Thus, the aggregate also acts as its own transactional boundary - all state changes need to be committed transactionally in a single atomic operation
* No system operation can assume a multi-aggregate transaction
* A change to an aggregate's state can only be committed individually, one aggregate per database transaction
* The need to commit changes in multiple aggregates signals an invalid transaction boundary and thus, inaccurate aggregate boundaries






