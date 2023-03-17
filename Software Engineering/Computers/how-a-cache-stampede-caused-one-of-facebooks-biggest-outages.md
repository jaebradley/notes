# [How A Cache Stampede Caused One Of Facebooks Biggest Outages](https://medium.com/better-programming/how-a-cache-stampede-caused-one-of-facebooks-biggest-outages-dbb964ffc8ed)

* A cache stampede occurs when several threads attempt to access a cache in parallel
* If the cached value doesn't exist, the threads will then attempt to fetch the data from the origin at the same time
* Feedback loop:
  * A large number of concurrent threads get a cache miss, so they all call the database
  * Database crashes due to enormous CPU spike and leads to timeouts
  * Threads receive the timeout errors and retry the request, causing another stampede

## Layering Caches to Mitigate Problem

* Multiple first layer (L1) caches that interface with applications that call a second layer cache (L2) which is the cache that actually calls the database for the value
* Even if a key on the L2 cache expires, L1 caches above it may still have the value stored, so this limits the threads that need to recompute the cached value
* Still vulnerable to a cache stampede for new content (like the follower stampede)
  * Celebrity uploads photo / video
  * All their followers view new content
  * Since content is new, it hasn't be cached

## Using Promises

* Instead of caching the actual value, cached Promise that will eventually provide the value
* When cache is accessed atomically and missed, instead of going to backend service to get fresh value, create a Promise and insert it into the cache
* Promise encapsulates the logic to get value from backend
* Other concurrent requests will not miss, but instead find the existing Promise - all workers will wait on single backend request represented by that Promise
* THe first thread to get a cache miss will create and cache an asynchronous promise using an atomic operation
  * All sequential fetch requests will immediately return the promise
  * Still need a lock to prevent multiple threads from accessing the cache key
  * But assuming that creating a promise is a near-instantaneous operation, the length of time threads stay in a spinlock is negligible
