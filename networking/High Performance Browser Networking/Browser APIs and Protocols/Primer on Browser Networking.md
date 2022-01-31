# Primer on Browser Networking

## Connection Management and Optimization

* Sockets are organized in pools, which are grouped by origin
* Each socket pool enforces its own connection limits and security constraints
* Pending requests are queued, prioritized, and then bound to individual sockets within a pool
* Unless the server intentionally closes the connection, the same socket can be automatically reused across multiple requests
* Origin is the combination of application protocol, domain name, and port number
  * Note that http, www.example.com, port 80 is a different origin than http, www.example.com port 443
* In practice, all major browsers limit the maximum pool size to six sockets
* Socket pooling allows the browser to service queued requests in priority order
  * To reuse sockets to minimize latency
  * Proactive opening of sockets in anticipation of a request
  * Idle sockets can be closed
  * Can allocated bandwidth across all sockets
