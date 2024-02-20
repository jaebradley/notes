# [Cache warming: Agility for a stateful service](https://netflixtechblog.com/cache-warming-agility-for-a-stateful-service-2d3b1da82642)

* Strategy for adding a new cache
  * Dual-write to the existing cache and the new cache
  * Allow data to expire in old cache based on TTL value
  * Doesn't work well for clusters that had items with no expiration time
  * Pay extra maintentance cost for clusters that did not experience mutations

## Key Dumps

* Dumped keys and metadata from each node and uploaded them to S3
* Key dump is consumed by a cache populator application
 * Downloads key dump from S3
 * Fetches data for each key from existing node
 * Populates new nodes with key + value
* Cache populator throughput impacted existing cache performance due to network congestion
 * Limits ability to run cache warming during high peak hours

## Cache Warmer Controller

* Controller creates a dedicated SQS queue to communicate between the Cache Dumper and the Cache Populator
* Initiates cache dump on source nodes
* Controller creates a new Populator cluster
* Once the SQS queue is empty and all messages have been consumed successfully by the Populator, the Controller will destroy the Populator cluster, the SQS queue, and any other resources

## The Dumper

* Enumerate keys using memcached LRU Crawler and dump keys into many key-chunk files
* For each key in the key-chunk file, the Dumper retrieves its value (and any metadata) and dumps them into a local data-chunk file
* Once max chunk size is reached, the chunk is uploaded to S3
* SQS message containing the chunk's S3 URI (along with other metadata) is published to the SQS queue
* Parallelize data dumps with workers taking a key chunk

## The Populator

* Consumes messages off of an SQS queue
* Downloads the S3 file associated with the S3 URI contained in the message
* Starts populating data chunk on destination replica
* Cluster autoscales based on available data chunks in the SQS queue

## Instance warmer

* EVCache uses consistent hashing with virtual nodes
* On startup, an EVCache node will need to be populated based on the data that is distributed across all the nodes
 * Data from all nodes needs to be inspected
 * Only the data for the keys that will be hashed to the new node will be dumped
* Populator consumes data-chunks to populate data to the specific node

## EBS Storage

* Large caches require large network bandwidth requirements for both ingress data into S3 and egress data out of S3
 * Netflix observed network bandwidth throttling after a certain period of time
* Use EBS-backed storage to store and retrieve data chunks
* Dumpers can attach to EBS volumes and dump data to a known location
* Populators can attach to the same EBS volume and start adding data to the new replicas
* Need to attach multiple instances to the same EBS volume to run the Dumper and Populator concurrently in order to achieve quick warm up
