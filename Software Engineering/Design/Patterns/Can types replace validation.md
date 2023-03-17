# [Can types replace validation?](https://blog.ploeh.dk/2022/08/22/can-types-replace-validation/)

```java
public Reservation(
    Guid id,
    DateTime at,
    Email email,
    Name name,
    NaturalNumber quantity)
    
public Reservation(
    Guid id,
    DateTime at,
    Email email,
    Name name,
    int quantity)
```

* Less explicit information available (can I pass zero as `quantity`? Can I pass `-1`?)
  * If only first constructor, it's clear what the answer is
  * Centralize runtime checks in one place - no defensive code across code base
    * Any code that uses `NaturalNumber` knows that the check has already been performed

## Predicative and constructive data

* Since `NaturalNumber` relies on a runtime predicate, it is a type called "predicative"
  * Fail to take full advantage of the type system
  * Unfortunately, `new NaturalNumber(-1)` will compile even if it fails at runtime, so it's representable
    * In the negative, is it possible to make illegal states unrepresentable?

## Validation as functions

* Given the data, is it well-formed and according to specification
  * If you have to query a database before making a decision, you're not validating the input, you're applying a business rule
  * As a rule of thumb, validations should be pure functions

## Error handling

* There may be requirements where all errors should be returned as one collection
* If a `NaturalNumber` is invalid, the logic short-circuits and further data validation stops
* Same is true for the `Either` monad, it short-circuits, since once a failure is detected no further processing takes place

## Monoidal versus applicative validation

* For constructive data, the question is "is a candidate value representable?"
* Predicative data is defined by predicates (functions that return a boolean)
* Boolean values are composable, but can short-circuit
  * A single false value could cause the `all` monoid (aka the `and` operator) to short-circuit and stop processing subsequent predicates
