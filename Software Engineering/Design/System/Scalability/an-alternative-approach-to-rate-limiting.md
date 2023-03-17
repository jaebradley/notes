# [An Alternative Approach to Rate Limiting](https://medium.com/figma-design/an-alternative-approach-to-rate-limiting-f8a06cf7c94c)

* Use Redis vs. relational database like Postgres since it's in-memory and thus faster

## Token Bucket

* Store last request timestamp and remaining request tokens
* Store as two values in Redis hash vs. two keys for memory efficiency
* For each new request, rate limiter would
  * Fetch hash from Redis
  * Refill the available tokens (if necessary) given the identified refill rate and time of the last request
    * So if the rate is three requests per minute and a minute has passed since the last request, refill the request tokens to three
  * Update hash with the current request's timestamp and new available token count
    * Decrement existing token count by one
  * When token count is at zero, any additional requests will have exceeded limit
* Downside is that the fetch + write behavior is not atomic
* If requests are being served from multiple servers and they are interleaved, two requests could fetch the same token count and allow more requests than are allowed
* Imagine case with single token remaining
  * Two requests come in at the same time
  * Both fetch data from Redis with the token count at one
  * They then both approve the existing request and update the token count to zero when in reality only one of the requests should have been approved
* Ways to achieve atomicity by using a Redis lock
* Lock would slow down concurrent requests

## Fixed Window Counters

* Hash that stores user id + timestamp as key and request count as value
* Hash has a set expiration time to remove it after rate limit window has become stale
* As requests come in for valid key (i.e. key with timestamp in window) the request count is incremented
* When request count reaches rate limit value, do not allow any more requests
* Downside is that it can lead to more requests at edges of window that are valid that, in totality, violate rate limit
* If rate limit is 5 requests per minute and user makes 5 requests at 11:00:59 they could make 5 more requests at 11:01:00 because counter starts anew each minute

## Sliding Window Log

* Optimizes for accuracy - stores timestamp in sorted set for each request
* The sorted values would represent the microsecond timestamp
* Easily allow the efficient removal of set members with outdated timestamps and count size of set afterward
* Sorted set's size would be equal to number of requests in most recent sliding window of time
* Order of operations are:
  * Add existing request's timestamp
  * Remove any expired timestamps
  * Check number of relevant requests
* Will keep track of requests even after user exceeds rate limit
* This side-effect will extend ban on malicious users
* Large memory footprint
  * 500 requests per day x 10,000 daily active users = 5 million values in Redis
  * Timestamp values are 4-bytes => 20MB of memory

## Sliding Window Counters

* Consider hourly rate limit with minute request counters
* The set of 60 counters are stored in Redis hash
* When each request increments counter in the hash, it also sets the hash to expire an hour later
* However, this expiration is for the hash and not for the hash's values
* So if user makes requests every minute, the hash may never expire even though there are stale minute counters
* These minute counters need to be flushed in some manner
* 4 bytes per counter x 60 counters x 10,000 users => 2.4 MB
