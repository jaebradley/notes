# [Eradicating Non-Determinism in Tests](https://martinfowler.com/articles/nonDeterminism.html)

* If you have non-deterministic tests, keep them in a different test suite to your healthy tests
  * Set up a numeric limit for number of tests that are quarantined before the tests must get cleared out
  * Another option is to put a limit on how long a test may be in quarantine (like no longer than a week)

## Lack of Isolation

* Either always rebuild your starting state from scratch or ensure that each test cleans up properly after itself
  * Often easier to write tests by always rebuilding and often easier to find source of the problem
  * With clean-up, one test will contain the bug while another test will fail, so it's hard to find the real problem
* When testing with a database, conduct tests within a transaction, and then rollback the transaction at the end of the test

## Asynchronous Behavior

* Common mistake is to do something like "make async call", "sleep", then "test response"
* Want to set the sleep long enough to give plenty of time for a response, but this can mean waiting for a response, slowing down tests
* Sometimes, the sleep won't be long enough and you'll get a false failure
* Instead, can have the asynchronous service take a callback, which it can call when done (best since it means never having to wait longer than necessary)
* Other option is to poll for an answer, until some limit occurs, at which point, can timeout the test

## Remote Services

* Create a test double that looks like the remote service, but only mimics the remote system's behavior
  * The double needs to be setup so that it provides the right kind of response in interaction with our system, but in a way that's controllable / deterministic
  * To ensure the double has (effectively) the same behavior as the remote system, run "Contract Tests" which execute the same interaction with the remote system and the double, checking that the double and the remote system "match"
* Many are against using test doubles in functional tests, believing that must test the real connection in order to ensure end-to-end behavior
  * Any advantage gained by talking to the real system is overwhelmed by the need to stamp out non-determinism

## Time

* Always wrap the system clock with routines that can be replaced with a seeded value for testing
* A clock stub can be set to a particular time and frozen at that time, allowing tests complete control over the clock
