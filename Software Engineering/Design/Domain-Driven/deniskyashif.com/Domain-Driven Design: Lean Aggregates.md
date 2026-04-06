# [Domain-Driven Design: Lean Aggregates](https://deniskyashif.com/2026/04/04/domain-driven-design-lean-aggregates/)

## Fat Aggregates
* Article's example is a `Project` model for a project management system
* `Project` model has *many* properties - tasks, team members, attachments, etc
* Procedural designs are where classes just hold data and logic lives in service classes
* Article's `Project` example is *not* strictly procedural and encapsulates behavior
  * So the `Project` class has an `AssignTask` method and an `AttachDocument` method and a `Complete` method
  * These methods make sense to put on the aggregate, and the logic protects invariants and prevents invalid state
* In DDD, repositories load the full aggregate before any write so all invariants can be verified
  * If `Project` is the central aggregate, then every write operation - assigning a task, adding a member, attaching a document - must load the entire object graph, leading to performance problems
* Also, invariably `Project` grows larger and larger turning into a bloated class that is hard to reason about and risky to change

## Trimming Down the Aggregate
* If two pieces of data do not need to change together, they probably should not live in the same aggregate
  * Attaching a document does not need tasks and team members loaded into memory
  * One approach maybe be to move documents and tasks to separate aggregates that reference a `ProjectID` 
* To support a business logic rule like "no documents can be attached if the project is completed" there needs to be a split in responsibilities
  * A domain service encapsulates the cross-aggregate business rule
    * `DocumentDomainService#EnsureCanAttach(Project project)`
  * An application service orchestrates repositories and persistence
    * `DocumentApplicationService` that depends on a project repository, document repository, and a document domain service
    * This service class has an `AttachDocument` method that calls the domain service

## Common Pitfalls
* A domain service should express business rules
* Managing transactions, persisting changes is data orchestration and belongs to an application service - avoid putting this orchestration in domain services
* Don't just consider database table boundaries when defining aggregates - if two entities must always be valid together in a single transaction, they should likely belong to the same aggregate
