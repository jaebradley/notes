# [GraphQL Mutation Design: Anemic Mutations](https://medium.com/@__xuorig__/graphql-mutation-design-anemic-mutations-dd107ba70496)

* Imagine an `UpdateCheckoutInput` with a bunch of optional fields
  * Reason for this was to allow partial updates but at the sacrifice of removing nullability constraints
  * GraphQL strengths is the type system so all validation is now deferred until runtime

```graphql
type Mutation {
  updateCheckout(input: UpdateCheckoutInput!): UpdateCheckoutPayload
}

input UpdateCheckoutInput {
  email: Email
  address: Address
  items: [ItemInput!]
  # etc
}
```

* The input type is very data-centric instead of behavior-centric
* Imagine if API user wants to use this mutation in conjunction with an `Add To Cart` button
* Mutation is focussed so much on data and not behavior that client needs to guess how to make a specific action
  * In other words, what if the action of adding an item to a checkout requires certain fields / attributes being set? Client would only learn about this at runtime or end up in an incorrect state by forgetting to update a particular attribute
* More cognitive overload for the client since they need to select / know the set of fields to update
* Because the input is focussed on the internal data of a Checkout and not the potential behaviors of a Checkout, it's not explicitly clear what actions are possible - it's implied via the data model
* Instead, a mutation called `addItemToCheckout` that takes an `AddItemToCheckoutInput` which is comprised of a `checkoutId` and an `item` is much more explicit
  * Each value is non-nullable, so client knows _exactly_ what to provide
  * Since the action is specific, the errors returned by server can be more specific as well
* This also means that backend implementation is more straight-forward because a lot of the validations that existed in the optional fields case are no longer needed
  * Another way of thinking about this to is imagine trying to emit an `ItemAdded` event every time a user adds an item to their checkout
  * In the big fat optional mutation case, need to check scenario with conditionals (when all these fields exist then a user has added an item to their checkout, but when all these other fields exist it's a different type of event) which gets messy