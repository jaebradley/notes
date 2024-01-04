# [Active-Active for Multi-Regional Resiliency](https://netflixtechblog.com/active-active-for-multi-regional-resiliency-c47719f6685b)

* Netflix deploys services across multiple instances and Availability Zones, as well as multiple AWS Regions

## Requirements

* Services are stateless, so data / state replication is handled in data tier
* Services must access resources locally in its Region
  * Applications that publish data into an S3 bucket now have to publish data into the same S3 buckets in a different region
* No cross-regional calls along the call path for a given user
  * Any kind of data replication is asynchronous
 
## Routing

* Users are geo-DNS routed to the closest AWS Region
* In the case of a significant region-wide outage, tools to override geo-DNS and direct all users to a healthy Region

## Zuul Enhancements

* A mis-routed user request is one that does not conform to Netflix's geo-directional records
  * Ensures a single user device does not span multiple regions
* Maximum traffic level definition
  * Additional requests are automatically shed (i.e. an error response is returned to the user)
  * This is to protect downstream services against a thundering herd of requests
  * Protects services that are scaling up to meet growing demand, or services with cold caches
 
## Data Replication

* Apache Cassandra has multi-directional and multi-datacenter asynchronous replication
* Data replication experiment
  * 1 million records written in one region
  * 500ms delay, before read issued in another region for the records written in the initial region
  * All million records were successfully read
* Memcached layer in front of Cassandra
  * Whenever a write occurs in one region, message sent to other region via SQS to evict the corresponding entry
  * Subsequent read in the other region will either cause a read through to Cassandra, and update the region's cache with the updated value
 
## Monkeys

* Chaos Gorilla - removes an entire Availability Zone
* Split-brain - severed connectivity between Regions
  * Testing that services in each Region functioned normally, even though data replication was being queued
* Chaos Kong - outage that would require shifting user traffic to another region
  * Users routed to the "failed" region would be redirected to the healthy region
