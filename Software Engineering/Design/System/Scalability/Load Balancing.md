# [Load Balancing](https://samwho.dev/load-balancing/)

## Least Connections

* Load balancer sits between server and user, so it can accurately keep track of how many outstanding requests each server has
* It knows which servers have the least work to do and prioritises those
* Default HTTP load balancing method for AWS's load balancers
* Only drops requests when there is no more queue space available

## Peak Exponentially Weighted Moving Average

* In example, leftmost server does not serve any requests
* Load balancer "learns" that all the other servers are faster, so there is no need to send requests to the slowest one
* Each server keeps track of latency from last N requests
* Sums latency values, but with exponentially decreasing scale factor
* So the older a latency value is, the less it contributes to the sum
* This exponentially weighted moving average is multiplied by the number of open connections to the server
  * The server with the smallest product is the server to send the next request to
