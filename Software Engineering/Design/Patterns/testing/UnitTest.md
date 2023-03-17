# [UnitTest](https://martinfowler.com/bliki/UnitTest.html)

* An important distinction is whether the unit you're testing should be sociable or solitary
* Imagine a `price` method on an `Order` class that calls methods on other product and customer classes
* "Solitary" unit tests will not call real product or customer classes, because any faults in the customer class would cause the order class to fail so instead test doubles are used
* Some "classicist" xunit testers argue that any collaboration with external resources, such as a database or a filesystem should use doubles
  * Party for non-determinism risk and party due to speed
