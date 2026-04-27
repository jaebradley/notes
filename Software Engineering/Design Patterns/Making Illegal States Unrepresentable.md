# [Making Illegal States Unrepresentable](https://buttondown.email/hillelwayne/archive/making-illegal-states-unrepresentable/)

* Example is a `Contact` with a `Name`, `EmailContactInfo`, and `PostalContactInfo` fields
  * Have a business rule where the contact must have an email or postal address
  * Can express this via a union of email only, postal address only, or email address and postal address
* Another example: elements in disjoint sets where no element can be shared between them
  * A person has friends and enemeies where a person cannot be friends and enemies with another person
  * To avoid the possibility of collision, replace the friends and enemies sets with a single "relationships" mapping between a person and an enumeration of `friend` or `enemy`
    * Since keys can't be duplicated, there can't exist an element having two distinct values at once
* Another example: list of entities that have a position
  * Want to guarantee that no entities collide by having the same position
  * Can instead map the entities by their position to guarantee that no two entities have the same position
* Saying "every contact must have either an email or postal address" is predicative: we take a universe of representable contacts and use a predicate to select a subset of the universe
* Saying "every contact is `EmailOnly`, `PostalOnly`, or `EmailAndPost` is constructive: building up the set of all valid contacts from base rules
* If the contact example had more requirements / more complicated rules, the constructive approach becomes more complicated than writing a function that tests whether or not a contact satisfies the requirements
  * The function / predicative approach is more composable
