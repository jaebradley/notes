# [Testing Microservices, the sane way](https://medium.com/@copyconstruct/testing-microservices-the-sane-way-9bb31d158c16)

* Running entire suite of microservices on local machine is not realistic
  * Brittle, hard to replicate issues on one machine vs. another machine
  * Takes up a lot of time and effort
  * Resource intensive - "two dozen services with own databases and dependencies on macbook"
* Testing is just a best effort verification of system that depends on making smart bets and tradeoffs
* "Shadowing" testing strategy involves recording a small percentage of production traffic and "replaying" it against test instances
* Alternative strategy of pushing to production and using monitoring to indicate if things have broken and if so, fixing or rolling back quickly
  * Depends on having good monitoring
  * Forces engineers to think about handling error conditions and error states gracefully
    * Coding and architecting for failure vs. success
* Pre-production testing is usually a best effort verification of a *small* subset of the guarantees of a system
* Individual microservices are usually backend "front-ends" that sit in front of a database or a cache
  * Most of the time, services support actions that are some type of I/O (reading from disk / writing to disk)
  * These I/O operations are thinly abstracted away at times and represent the core behaviors to test
  * What ends up happening is that the underlying I/O operations get mocked
* Unit testing with mocks makes it easier to effectively validate incomplete / flawed data model of service
  * Risk for confirmation bias
  * Mocks can complicate test code
* Assume microservices structure where service A is talking to service B
  * Test interaction between service A and B by spinning up (fake) service for B and test A's interactions against fake
* Fuzzing in tests is when "bad" or "invalid" data is inputted into an application to see if, and how, application fails in response to data
* Another benefit of unit tests is that it applies design pressure to structure APIs in a way that's easy to consume
  * If it's complicated to make a "fake" client / SDK version for testing, it's also probably a complicated API
* It's possible to build APIs with high-levels of code coverage with a lot of unnecessary abstraction
* Integration tests for complex systems involves a lot of overhead
  * Maintaining environments (i.e. keeping them in-sync) for local, staging, and production
