# Chapter 5. Implementing Simple Business Logic

* A system's public interface can be seen as as collection of business transactions that consumers can execute
* One way to ensure transactional behavior is to make operations idempotent (i.e. an operation leads to the same result even if the operation is repeated multiple times)
* Another way is to use optimistic concurrency control - prior to calling some operation, caller has read the value that they plan to update and passed this "current value" to the operation
  * The operation will only update the  property with the specified new value if the "current value" that is defined equals the one that was read by the caller

```java
public void Execute(Guid userId, long expectedVisits) {
  _db.Execute(@"UPDATE Users SET visits=visits+1 WHERE user_id=userId AND visits=@p2", userId, expectedVisits);
}
```

* Subsequent executions of `Execute` with the same input parameters won't change the data as the `WHERE` condition won't be fulfilled

## Active Record

### Implementation

* Pattern's goal is to encapsulate the complexity of mapping in-memory objects to a database's schema
* Active record objects can contain business logic, for example, validating new values assigned to fields, or even implementing business-related procedures that manipulate an object's data

### When to Use Active Record

* Essentially a transaction script that optimizes access to databases
* Difference between AR and transaction scripts is that AR addresses complexities around mapping data structures to a database's schema
* AR pattern is also known as the _anemic domain model antipattern_