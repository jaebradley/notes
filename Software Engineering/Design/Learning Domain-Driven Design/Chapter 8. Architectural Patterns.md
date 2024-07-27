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









