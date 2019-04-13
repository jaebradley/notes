# [Using Nullability in GraphQL](https://blog.apollographql.com/using-nullability-in-graphql-2254f84c4ed7)

* When a `null` is returned for a non-`null` field in a resolver, the `null` result bubbles up to nearest parent
* So if items in array have non-`null` field and that field is `null` for whatever reason, the array will contain one item (`null`)
  * Generally, `data` will still be returned but also with an `error` field
* So frontend code never has to check the nullability of that field - at the expense of not getting any data for the parent containing that field
* So with non-`null` fields need to decide if you'd rather have no result vs. partial data
* Three options with lists
  * All items within a list are not nullable, but list is nullable (i.e `null` value instead of empty array)
  * Items within a list are nullable but list must be empty array
  * Items are not nullable nor is the list itself
* Adding a non-null argument to a previously existing field is _not_ backwards-compatible since validation will fail for existing uses (since they don't have values for that field)
* Removing non-nullness for an output field is a breaking change since code can depend on field always existing, which is no longer the case
* Use null fields for data fetched from a separate source or asynchronously
  * If that data source fails, can still load partial data
* List items should be non-nullable
