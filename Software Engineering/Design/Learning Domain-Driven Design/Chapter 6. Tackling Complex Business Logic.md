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

