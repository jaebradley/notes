# [How we built rate limiting capable of scaling to millions of domains](https://blog.cloudflare.com/counting-things-a-lot-of-different-things/?ref=blog.pragmaticengineer.com)

* Edge of network rate limit
* Every request that matches customer-defined rule is counted per client IP address
* Once counter is exceeded, additional requests are not allowed to reach origin server, error page is returned to the client

## How to make counting fast and efficient?

* Reporting all counters to a single central point is not a realistic solution (high latency, central service availability guarantees)
* All edge server traffic is anycast traffic
  * Same IP address for a given web application worldwide
  * Traffic is automatically routed to the closest data center
  * Each new TCP connection might hit a different server inside a given data center
  * Counting system inside each data center

## Algorithm

* Use previous minute counter to calculate approximate request rate
* In example, 42 requests in the previous minute and 18 requests occurred 15 seconds into the current minute
  * Rate approximation is `42 * (60 seconds - 15 seconds) + 18` or `49.5` requests
* In practice, this algorithm is smoother than the fixed window method
  * 6% difference between "real" rate and approximated rate
* Very low memory usage
  * Two numbers per counter
  * Simple instructions (single `INCR` memcache command)
  * Rate calculation is not complicated (one `GET` memcache command and some very simple math)
* Increment jobs are run asynchronously
  * Legitimate requests are not slowed down
  * If the request rate is above the threshold, some information is stored asking all servers in the data center to start rate limiting the client
    * Only this information is checked during request processing
* Using the simple rate-limiting algorithm, the time when the rate limiting will expire is known
  * This time is cached within the servers' memory
  * Server will not run another query for the requests for that client until the expiry time has passed
