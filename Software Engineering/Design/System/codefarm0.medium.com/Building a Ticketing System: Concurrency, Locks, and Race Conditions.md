# [Building a Ticketing System: Concurrency, Locks, and Race Conditions](https://medium.com/@codefarm0/building-a-ticketing-system-concurrency-locks-and-race-conditions-182e0932d962#bce5--highlight-94e6f8a2308e)
* Pessimistic locking runs into issues like blocking other requests, even read requests, dead lock potential if locking multiple rows
  * `SELECT FOR UPDATE` only locks within a single database connection
  * If two servers query different database replicas they may both be able to get the same seat
* Optimistic locking assumes conflicts are rare
  * This approach uses an incrementing version ID to detect conflicts
  * Does not scale when there is a flash sale where retries from a version conflict will create more load
* Distributed lock through Redis
  * Different servers can check the same Redis instance
  * Lua script solves for the following edge case:
    * Server 1 acquires lock, sets value to `uuid1`
    * Server 1 takes 35 seconds (lock expires after 30s)
    * Server 2 acquires lock, sets value to `uuid2`
    * Server 1 finishes, tries to delete lock using `uuid1`
    * Without Lua script, Server 1 might delete Server 2’s lock!
  * Lua scripts are executed [atomically](https://redis.io/docs/latest/develop/programmability/eval-intro/)
  * If Server 1 crashes while holding the lock, the TTL will expire at some point
* Redlock Algorithm:
  * Client gets current time T1
  * Try to acquire lock on N Redis instances (N = 5 typical)
  * Lock is valid if acquired on majority (⌈N/2⌉ + 1)
  * Calculate lock validity time = TTL — (T2 — T1) — clock drift
  * Release lock on all instance
* Consider separating hot paths and ownership boundaries when identifying service separation
  * This allows the hot paths to be scaled independently
  * For example, if the search queries per second are far greater than bookings per second, scale the search and booking services independently
* Also consider the type of required consistencies when defining service boundaries
  * For example, inventory and booking services might require strong consistency whereas a search service may only enforce eventual consistency
