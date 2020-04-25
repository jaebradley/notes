# Item 26: Bound `retry` Attempts, Vary Their Frequency And Keep An Audit Trail

* Certain exceptions (like retrying API calls), can be safely handled by rescuing the exception and restarting the enclosing scope with `retry`
* Writing an unconditional `retry` is akin to writing an infinite loop - instead use a combination of a max number of retry attempts and exponential backoff
  * When defining this max retry limit, define and initialize the variable outside of the `begin` block - if you initialize it _inside_ the `begin` block, it would be reset to `0` each time the `retry` restarts the `begin` block
  * After, the max retry limit is hit, re-raise the exception (tried to handle the exception as best as possible - and better than just swallowing the exception)
  * Make sure to record the fact that an exception happened before using `retry`