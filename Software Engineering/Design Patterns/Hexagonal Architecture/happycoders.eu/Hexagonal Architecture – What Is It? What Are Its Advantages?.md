# [Hexagonal Architecture – What Is It? What Are Its Advantages?](https://www.happycoders.eu/software-craftsmanship/hexagonal-architecture/)

## Disadvantages of the Layered Architecture
* Classic layered architecture has an organization strategy that looks something like `client -> presentation layer -> business logic layer -> data access layer -> database`
* Because all entities, repositories, and ORM libraries are also available at the presentation layer, this can tempt developers to let the boundaries between layers weaken
* The coupling between different layers can also make upgrading databases or a new ORM library version difficult as such an update would require adjustments in all layers of the application

## What is Hexagonal Architecture?
* Application should be controllable by users, other applications, automated tests - should be agnostic of invocation pattern (i.e. invoked from a user interface vs. a REST API vs. a test framework)
* Business logic should be developed and tested in isolation from the database, other infrastructure, and third-party systems
  * It should make no difference to the business logic whether the data is stored in a relational DB, NoSQL, XML files, proprietary binary format, etc
* Infrastructure modernization (like upgrading the database server, upgrading libraries, etc) should be possible without adjusting the business logic

## Ports and Adapters
* Business logic = application in hexagonal architecture parlance
* This business logic (application) defines interfaces (ports)
* Example is a user interface that submits a form for a new user
  * There could also be a REST interface that also submits a form via an HTTP POST request
  * This would be translated by the Database Adapter to save the user via a `INSERT INTO users...` SQL query
* The ports that control the business logic are called "primary" or "driving" ports / adapters
* The ports that are controlled by the application are called "secondary" or "driven" ports / adapters

## Dependency Rule
* All source code dependencies must only point from the outside inwards - _towards the business logic application_

<img width="1600" height="916" alt="image" src="https://github.com/user-attachments/assets/6ff89304-128f-4aa9-8dc6-5eae59e404f0" />

* Example of how this works for the user registration case from a higher level of source code dependency + control flow
  * There is a `RegistrationController` (adapter) that uses the `RegistrationUseCase` interface (primary port definition)
  * The `RegistrationService` implements this `RegistrationUseCase` interface

## Dependency Inversion
* Used when implementing secondary ports and adapters
  * The rule is that source code dependency should always point inwards (the business logic / application depends on some database adapter) while the control flow (i.e. what logic is executing) moves from the core business logic / application towards the secondary port / adapter
* The `RegistrationService` (business logic / application) uses the `PersistencePort`  interface
* The `PersistenceAdapter` implements the `PersistencePort` interface
* When using an ORM, entity classes have a 1-1 mapping with database tables
  * Since the application core should not know the technical details of the database layer, the the ORM entity can't be defined in the application core
  * However, this ORM entity also can't be only defined in the `PersistenceAdapter` layer because the application core cannot access it since the only interaction between the `PersistenceAdapter` and the `RegistrationService` should be via the `PersistencePort` interface
* Solution is to create an additional model class in the adapter that does not contain any business logic, but contains all the technical annotations / mapping logic to go from the persistence layer to some model object
  * The adapter then maps the core application model to the adapter model class and vice versa
