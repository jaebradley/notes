# [CircuitBreaker](https://martinfowler.com/bliki/CircuitBreaker.html)

* Software systems can make remote calls to software running in different processes, probably on different machines across a network
* Big differences between in-memory calls and remote calls is that remote calls can fail or hang without a response until some timeout limit is reached
* If there are many callers of an unresponsive supplier, then you can run out of critical resources leading to cascading failures across multiple systems
* Circuit breakers wrap protected functions in an object that monitors failures
  * Once failures hit a certain threshold, the circuit breaker "trips" and all further calls to the circuit breaker return with an error, without the protected function executing
* Simple circuit breaker implementation keeps track of an input function to execute, a timeout value, and some failure threshold (after which the circuit is "tripped" and errors will be returned)
  * When the circuit is "closed", the underlying input function is attempted, the result is recorded in the case of a timeout, and the timeout error is re-raised
  * Any successful request resets the failure count to 0
  * Once in the "open" state though, the circuit breaker implementation will never attempt the underlying input function
* The resetting circuit breaking implementation has a third state - half open - where calls are attempted if the difference between current time and the last failure time is greater than some threshold input value
* Circuit breakers can be used for asynchronous communication. For example, put all requests on a queue, which are consumed at some rate to avoid overloading the server. The circuit will "break" when the queue is filled.
