# 5.6 Building an efficient, scalable result cache

## First Attempt

* Use a `HashMap` to store the results of previous computation
* The `compute` method checks if result is cached, and return the precomputed value if it is, else, compute the result, put it in the `HashMap` cache, before returning
* Since `HashMap` is not thread-safe, have to synchronize the `compute` method but obviously only one thread can execute the `compute` method
  * Multiple threads can be queued up waiting to execute `compute`

## Second Attempt

* Replaces `HashMap` with `ConcurrentHashMap` and since `ConcurrentHashMap` is thread-safe, there is no need to synchronize when accessing the `Map` storing the precomputed values
* Two threads calling `compute` at the same time could end up computing the same value
  * For memoization, this is merely inefficient - as a general-purpose caching mechanism, for an object cache that is supposed to provide once-and-only-once initialization, this vulnerability could pose a safety risk
* If one thread starts the expensive computation because the key is not in the `Map`, then other threads may also start the same computation
* Need some notion of "Thread X is in the process of computing a result for key Y" so that other threads can check to see if / when Thread X's computation has finished and what the computation result was
* `FutureTask` represents a computational process that may or may not have completed and `FutureTask.get` returns the result of the computatoin immediately if it is available or blocks until the result has been computed and then returns it

## Third Attempt

* Use a `ConcurrentHashMap<A, Future<V>>` instead of `ConcurrentHashMap<A, V>`
* Check if computation has been started by checking if there's a `Future` in the `Map` for the given key
* If computation hasn't started, creates a `FutureTask`, registers it in the `Map`, and starts the computation
* If the computation has started, it waits for the result of the existing computation by calling `get` on the `Future` (which may return immediately if the computation has finished, or if the computation is still on-going, may block until the computation finishes)
* Still a window where two threads might compute the same value, but the `if Future exists in Map for key` check is still a nonatomic check-then-act sequence (where the "act" is creating a `FutureTask` representing the computation)
  * There is an atomic `putIfAbsent` method of `ConcurrentHashMap`
* If a computation is failed or cancelled, future attempts to compute the result will indicate cancellation or failure
  * Instead, the `Future` representing the result should be removed if the computation was cancelled
  * It may also be desirable to remove the `Future` upon detecting a `RuntimeException` if the computation might succeed on a future attempt
