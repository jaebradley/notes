# Chapter 8. Architectural Patterns

## Layered Architecture

* Codebase is organized into horizontal layers
  * Each layer addresses one of the following technical concerns: interacting with consumers, implementing business logic, persisting data
  * Classic form is a presentation layer, business logic layer, and data access layer

### Presentation Layer

* Implements program's user interface, could be a web interface or a CLI
* In other words, the program's public interface

### Business Logic Layer

* Business logic like active records or domain models are implemented here

### Communication Between Layers

* Top-down communication model: each layer can hold a dependency only on the layer directly beneath it
* Decouples implementation concerns and knowledge shared between layers
* Presentation logic only references business logic layer
  * Has no knowledge of the design decisions made in the data access layer

### Service Layer

* This layer is often added to the "layered architecture pattern" and is described as

> Defines an application's boundary with a layer of services that establishes a set of available operations and coordinates the application's response in each operation

* Acts as an intermediary between the program's presentation and business logic layers
* Example is an MVC controller that creates a new user
  * It exposes an endpoint, creates a new User active record object, saves it, orchestrates a database transaction to ensure a proper response is generated in case of an error
* Service layer acts as a facade for the business logic layer
  * It exposes an interface that corresponds with the public interface's methods
  * This interface encapsulates the required orchestration of the underlying layers
* Another benefit of the service layer is that the same service layer components can be reused to serve multiple public interfaces like a GUI as well as an API
* Service layer is generally required if the business logic pattern requires orchestration, like with the active record pattern
* Service layer effectively implements the transaction script pattern, while the manipulated active records are located in the business logic layer
* Service layer = application layer
* Business logic layer = domain layer = model layer

### When to use Layered Architecture

* Good for systems that have an active record pattern
* Challenge to implement domain model as the business entities (aggregates, value objects) have no dependencies and no knowledge of the underlying infrastructure

## Ports & Adapters

* The presentational and data access layers represent integration with some "external" components
  * Databases, user interface frameworks, etc - implementation details that do not reflect the system's business logic
  * These "infrastructural" cocnerns can be distilled into a single "infrastructure" layer

### Dependency Inversion Principle

* High-level modules, which implement business logic, should not depend on low-level modules
  * The DIP is violated in traditional layered architecture - the business logic layer depends on the infrastructural layer
  * Entites / Active Records depend on the undelrying database
* With this principle in mind, the business logic layer does not depend on any of the system's infrastructural components
* Application layer that acts as a facade over the system's public interface describes all the system operations, orchestrating the system's business logic and execution
* Infrastructure layer (DB, UI framework) depends on the application layer (various actions) which depend on the business logic layer (entities)

### Integration of Infrastructural Components

* Instead of referencing / calling the infrastructural components directly, the business logic layer defines "ports" that are implemented by the infrastructure layer
* The infrastructure layer implements "adapters" or concrete implementations of the port interfaces when working with different underlying technologies (a DB vs. a cloud-based object storage solution)
* Abstract ports are resolved into concrete adapter implementations through dependency injetion / bootstrapping

## Command-Query Responsibility Segregation

* Multiple persistence layers might be used to implement different data-related requirements
* Document store for operational database, column store for analytics/reporting, and a search engine for search-based capabilities

### Command execution model

* Single model to execute operations that modify system state (i.e. system commands)
* Implements business logic, validates rules, enforces invariants
* Only model representing strongly consistent data - system source of truth

### Read models (projections)

* Precached read-only (i.e. non-modifiable) projections that can be regenerated from scratch

### Projecting Read Models

* Similar to materialized views in relational databases
* Whenever the source tables are updated, the changes should be reflected in the precached views

#### Synchronous projections

* Projection engine queries OLTP database for added or updated records after last processed checkpoint
* Projection engine uses the updated data to regenerate/update the system's read models
* Projection engine stores the checkpoint of the last processed record
  * Checkpoint value will be used during next iteration for getting records added / modified after the last processed record

#### Asynchronous projections

* Command execution model publishes all committed changes to a message bus
* System's projection engines can subscribe to the published messages, and use them to update the read-only models

### Model Segregation

* Common misconception is that CQRS-based systems should never return any data
* Command should return information to the caller about whether the command succeeded or failed (and why it failed)
* Returned data should originate from the strongly-consistent data model and _not_ from projections






