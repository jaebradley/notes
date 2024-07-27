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