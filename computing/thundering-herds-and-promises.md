# [Thundering Herds & Promises](https://instagram-engineering.com/thundering-herds-promises-82191c8af57d)

* Backend that service calls is slow
  * 90% of requests are the same, so introduce a cache
  * Before calling backend, check cache to see if value is present
* If cache is erased because had to restart the service, make the cache external
  * Use memcache
  * Use local disk that service can load cache data from on startup
* If the service is a brand new feature, could deploy the new client so that not all instances of the service are turned on at once
  * Have service perform some type of warmup, perhaps with fake requests to emulate what is expected
* If there are lots of instances of this service, an existing instance could be communicated with to accelerate cache warmup in future
* Thundering herd problem: if cache is hit with 100 concurrent requests and the cache is empty, then all of them get a cache-miss at the same time, resulting in 100 individual requests to the backend
* Instead of caching the actual value, we cached a Promise that will eventually provide the value
  * When the cache is used atomically and gets a miss, instead of going back to the backend, a Promise is created and inserted into the cache
  * This Promise represents the work to retrieve data from the backend service
  * The benefit this provides is other concurrent requests will not miss as they'll find the existing Promise - and all these simultaneous workers will wait on the single backend request
