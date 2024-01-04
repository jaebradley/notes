# [Ephemeral Volatile Caching in the cloud](https://netflixtechblog.com/ephemeral-volatile-caching-in-the-cloud-8eba7b124589)

* Cache is spread across multiple instances
* AWS Zone-Aware, with data replication across zones
* Registered with Netflix's internal naming service for node/service discovery
* Reads from cache service are served from within the same AWS zone
  * This avoids data egress fees for reads
* Data inconsistency can occur
  * Traded speed for consistency
  * Applications that depend on the cache can handle inconsistency
  * Built-in consistency checkers
  * Short TTL for inconsistent data
* When cache service instances fail, cache misses are minimal as consistent hashing is used to shard data across the cluster

## Under the Hood

* Java Sidecar app communicates with the discovery service and hosts admin pages
* Health and monitoring applications
* Java client discovers cache servers
  * Automatically handles cases where servers are added or removed from clusters
  * Client replicates data during modification operations
  * Client gets data from server in the same zone as the client

## Single Zone Deployment

* Server instance registers with service discovery on startup
* Webapp initializes cache client library on startup

## Multi-Zone Deployment

* Cache server instance in Zone-A registers with service discovery in Zone-A and Zone-B
* Cache client library looks up all the registered cache server instances across all zones
* When a web app in Zone-A needs to read data for a key, cache client looks up the cache server instance in Zone-A and fetches data from this instance
* When a web app in Zone-A needs to modify data for a key, cache client looks up the cache server instance in Zone-A and Zone-B that is the "owner" (via consistent hashing) for this key and modifies the key for that instance

## Example

* Web app needs a list of similar movies or TV shows
* Web app tries to read this data from cache service - hit rate ~99.9%
* On cache miss, web app calls the Similars application to compute this data
* Data could have been previously computed, but missing from the cache
  * Similars app will read it from SimpleDB
* Computed data is written to the cache service and then returned to the client
