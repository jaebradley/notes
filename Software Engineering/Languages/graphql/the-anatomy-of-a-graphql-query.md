# [The Anatomy of a GraphQL Query](https://blog.apollographql.com/the-anatomy-of-a-graphql-query-6dffa9e9e747)

* GraphQL document can have multiple operations and fragments
* Operation is a single query, mutation, or subscription
  * Type associated with operation can only be `query`, `mutation`, or `subscription`
  * Variables can be validated by GraphQL schema definition (since it's static)
  * Variables are passed separately from the query document in a transport-specific way - usually as a separate key/value field in the `JSON` sent to the server
  * Selection set is the set of fields requested in an operation
    * A selection set must exist for any field that returns an object type
    * Conversely, a selection set is not allowed on scalar types

## Fragments

* Fragment names within a document must be unique
* Fragments can be used in any selection set - in order to provide validation, a type condition needs to be specified
  * `fragment ABC on SomeType`
* Fragments are used in operations using a spread syntax like `...ABC`
* Fragments can also be defined in-line like `...on SomeType`
  * In-line fragments are not required to have a type condition

## Directives

* An annotation on a field, fragment, or operation that affects how it is executed or returned
  * `someType @skip (if: $someCondition)`
  * `@skip` is the directive which takes arguments
  * Arguments are handled by execution engine instead of being passed down to the actual field resolver

