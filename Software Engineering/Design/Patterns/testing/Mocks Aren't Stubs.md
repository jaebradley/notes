# [Mocks Aren't Stubs](https://martinfowler.com/articles/mocksArentStubs.html)

## Regular Tests

* Implementation is to taken an order object and fill it from a warehouse object
* Order has a product and a quantity
* Warehouse has inventories for different products
* If there's enough product in the warehouse, the order becomes filled and the warehouse's amount of the product is reduced by the appropriate amount
  * If there isn't enough product, the order isn't fille and nothing happens in the warehouse
* Tests are to test the `Order#fill` method, but in order to do that, a `Warehouse` instance is needed (i.e. `Warehouse` is a collaborator)
* The `Warehouse` instance is also needed to verify that an `Order#fill` could have potentially changed the state of the `Warehouse`
* This style of testing is state verification which means that we determine whether the exercised method worked correctly by examining the state of the Subject Under Test, and its collaborators, after the method was exercised

## Tests with Mock Objects

* Setup phase is divided into data and expectations
* The data setup phase sets up the objects we are interested in working with where the `Warehouse` instance is now a "mock" `Warehouse` (technically an instance of the `Mock` class)
* The expectations setup phase indicates which methods should be called on the mocks when the Subject Under Test is exercised
* In addition to the same assertions on the state of the `Order` object that existed for the "Regular Tests", there is also verification on the mocks, which check that they were called according to expectations
* Mocks use behavior verification, where there's a check to see if the order made the correct calls on the warehouse
* Tell the mocks what to expect during setup and ask the mocks to verify itself during verification
* Only the order is checked using asserts, and if the method doesn't change the state of the order, there's no asserts at all

## The Difference Between Mocks and Stubs

* The "Regular Tests" use a real warehouse object and the second case uses a mock warehouse 
* Fake objects actually have working implementations but usually take some shortcut which makes them unsuitable for production (like an in-memory database)
* Stubs provide canned answers to calls made during the tests, usually not responding at all to anything outside what's programmed for the test
* Spies are stubs that also record information based on how they were called (email service that records how many messages it was sent)
* Mocks are objects that are pre-programmed with expectations which form a specification of the calls they are expected to receive
* Only mocks insist upon behavior verification - the other doubles usually use state verification
* To illustrate a stub imagine sending an email message after failing to fill an order
  * Obviously don't want to send email messages to customers during testing
  * Instead, create a test stub for the email system that keeps track of the messages that were "sent" in an in-memory `List`
  * Then when the `Order#fill` method is sent, can verify with the stub that a single message was sent
  * If a mock was used instead of a stub, instead, would verify that the mock expects a single call to the `send` method

## Classical and Mockist Testing

* Classical TDD style uses real objects if possible and a double if it's awkawrd to use the real thing (a "real" `Warehouse` and a double for the mail service
* Mockist TDD practitioner will always use a mock for any object with interesting behavior (so a mock for both `Warehouse` and the mail service)

## Choosing Between the Differences

* There are some cases where it's hard to use state verification even if the underlying subject under test doesn't have any awkward collaborations
  * A cache is a good example, because the point of a cache is that you can't tell from it's state whether the cache hit or missed
  * In the case of a cache, behavior verification would be a wise choice

### Fixture Setup

* With classic TDD, you have to create not just the subject under test, but also all of the collaborators that the subject under test will interact with
* Mockist tests only need the subject under test and mocks for immediate collaborators, which can avoid some involved work around building up complex fixtures
* Can reuse complex fixtures by putting fixture setup code into a setup method or if used across several classes, in special fixture generation classes

### Test Isolation

* Mockist testing bugs will only cause tests whose subject under test contains the bug to fail
* Classicist testing will cause any tests of the client objects to also fail causing many failing tests across the system
* Mockists will say that this is a problem, since it's a pain to find the root of the error, however, Classicists will say it's easy to identify the culprit / source of the problems
* In essence, classic unit tests are also mini-integration tests, and many people like the fact that client tests may catch errors, probing at the areas where classes interact
* Mockists tests can also have incorrect expectations which results in passing unit tests that mask inherent errors

### Coupling Tests to Implementations

* Mockist tests test the "outbound" calls of the subject under test to ensure it talks properly to its suppliers
* Classic tests only care about the final state - not how that state was derived
* Mockist tests are more coupled to the implmentation of a method - in other words, changing the nature or order of calls to collaborators usually causes a mockist test to break
* Classicists think it's important to only think about what happens from the external interface and to leave all consideration of implementation until after you're done writing the test
