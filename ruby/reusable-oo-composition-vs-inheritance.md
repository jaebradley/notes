# [Composition vs. Inheritance](https://robots.thoughtbot.com/reusable-oo-composition-vs-inheritance)

* Objects should have high cohesion (i.e a single responsibility) and low coupling with other objects
* Composition-based solutions break up behavior into distinct responsibilities and encapsulate the implementation of each into different objects
* Composition-based solutions generally depend on public interfaces to communicate intent - this matches a general principle of coding to an interface and not an implementation
  * This lowers coupling and allows for handling special cases by passing in an object with a different implementation but the same interface
* Downsides of composition
  * Adds more indirection into the system
  * More work to assemble all components together in the desired behavior
  * Use inheritance when your object already has a single responsibility and you really do just want specializations
  * Use inheritance when you don’t have a choice such as when building `ActiveRecord` models
