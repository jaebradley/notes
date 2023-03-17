# [Load Balancers and Consistent Hashing in 6 Minutes](https://medium.com/better-programming/load-balancers-and-consistent-hashing-in-6-minutes-b5fc460aea4e)

* If simply hashing user IP address and then taking modulo with respect to number of servers to determine which server to load balance to, this approach does not elegantly support changing number of servers since it would change the modulo value
  * This is problematic because servers often-times store user-related data in their cache
  * So users that are re-routed to a different server will need the cache on the previous server to be invalidated
  * The cost of invalidating these caches maybe really high, especially when dealing with thousands of servers
* Consistent hashing
  * Think of each index on the circular array as a "slot" for an incoming request
  * Imagine distributing the existing servers around this circular array equally (maybe even using same hashing function for IP addresses that was mentioned before)
  * As requests come into their appropriate slot, they'll be served by the right-most server (relative to their slot index)
  * The impact of this approach is that adding or removing a server will only impact, at most, one other server
    * If a request in slot `88` was served by the server on slot `99` but then a new server was spun up on slot `95`, the request in slot `88` would now be served by this new server
    * So the previous server on slot `99` would need to invalidate it's cache, but _all other servers would not need to invalidate their caches_
  * Similar concept for removal (i.e. the server's right neighbor would take over the load)