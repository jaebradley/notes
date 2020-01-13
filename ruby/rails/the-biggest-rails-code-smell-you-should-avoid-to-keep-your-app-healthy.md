# [The biggest Rails code smell you should avoid to keep your app healthy](https://medium.com/planet-arkency/the-biggest-rails-code-smell-you-should-avoid-to-keep-your-app-healthy-a61fd75ab2d3)

* Callbacks destroy linear flow of code
  * Without controllers, can simply read code sequentially starting from controller through model / class methods
  * With callbacks, need to keep in mind _when_ they are executed, since they can be conditional
  * While a single callback may not be difficult to reason about, when there are many callbacks coupled together, that may increase complexity
* Imagine an `after_create` callback that is conditional on a certain column being populated (like sending a first-time welcome email on user creation)
  * This conditional logic is "hidden" at the model level, so no way to see this in the controller
  * Alternative solution is to create two model class methods that act as factory methods for the two states (creating a model instance and sending a welcome email vs. not)
  * Then let controller define a conditional to determine which method to call
  * Thus any branching logic is at the highest level possible
  * Probably even better to exract service object
