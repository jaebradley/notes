# [Why anemic models are considered as anti-pattern and how to replace it?](https://www.reddit.com/r/dotnet/comments/1evlvxq/why_anemic_models_are_considered_as_antipattern/)
* Domain-driven design with functional languages / paradigms require anemic models
* One [commenter](https://www.reddit.com/r/dotnet/comments/1evlvxq/comment/litzu7u/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button) mentioned that anemic model's are an anti-pattern because they move all control logic outside of the model
  * By moving logic outside of the model, this means there are no "rules" for achieving the same state
  * Commenter uses an example with an `Order` entity with a `Status` enum that can have a variety of values
  * If `Order` was an anemic domain model, there is logic outside of the `Order` class that is setting the value of `Order.Status`
  * There might be two different implementation paths that set `Order.Status` contained in two different modules
  * There may also be business logic (side-effects) associated with if `Order.DeliveredAt` is has some property, to send a customer email letting them now that the order was delivered
    * While this business logic might be encapsulated in a `OrderStatusUpdater` service, this logic sits outside the domain model
  * By contrast, the `Order` model could have an `UpdateStatus` method where all the business logic for various state transitions is implemented
  * Anemic domain models are an anti-pattern because you need to know `1 + n` classes to use the domain model (`Order` **and** `OrderUpdater`)
    * Additionally, any caller can set the `Status` to any value without invoking the correct logic and synchronizing other fields and data that need to be updated simultaneously
* [A comment](https://www.reddit.com/r/dotnet/comments/1evlvxq/comment/liwh0yc/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button) to the above commenter pointed out that rich models make it difficult to dependency inject some service (especially a third-party API)
