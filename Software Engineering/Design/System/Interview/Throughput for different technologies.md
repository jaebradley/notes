# Throughput

## Redis
* Single instance: More like 100-200k operations per second
* 5.3 million operations per second (Redis Enterprise) on c5.18xlarge EC2 instances with sub-millisecond latency
* From [Redis's Benchmarks page](https://redis.io/docs/latest/operate/oss_and_stack/management/optimization/benchmarks/) 
>"Regarding the bandwidth, it is generally useful to estimate the throughput in Gbit/s and compare it to the theoretical bandwidth of the network. For instance a benchmark setting 4 KB strings in Redis at 100000 q/s, would actually consume 3.2 Gbit/s of bandwidth and probably fit within a 10 Gbit/s link, but not a 1 Gbit/s one. In many real world scenarios, Redis throughput is limited by the network well before being limited by the CPU. To consolidate several high-throughput Redis instances on a single server, it worth considering putting a 10 Gbit/s NIC or multiple 1 Gbit/s NICs with TCP/IP bonding."

>"Being single-threaded, Redis favors fast CPUs with large caches and not many cores."

>"Redis runs slower on a VM compared to running without virtualization using the same hardware. If you have the chance to run Redis on a physical machine this is preferred."

## Databases
* PostgreSQL
  * 72 cores, 3 TB Ram
  * ~30k transactions per second
  * ~150k read requests per second
  * Similar numbers for a 12 core / 252 GB (disk) machine

## DynamoDB
* Single-millisecond operation latency
  * 2-3 ms for reads and writes
  * p99: ~4.5 ms
* Peak traffic of ~500k operations per second
* 6-8k write capacity units + 18k read capacity units for sustained periods
* DAX delivers 10x performance improvement via in-memory caching

## Webservers
* Nginx can handle 35k requests per second
* 10k concurrent requests
* ~2.5MB memory per connection

## Messaging
* Kafka
  * 2 million writes per second on three machines
  * 1 million producer messages per second
* [Apache Pulsar performance analysis](https://streamnative.io/blog/inside-apache-pulsars-millisecond-write-path-a-deep-performance-analysis)
  * Test persisted messages across replicas in 0.3 milliseconds and throughput of over 1.5 million messages per second on a single producer thread
  * Most of broker latency is spent in network I/O operations like sending data to bookies and waiting for responses
  * NVME disk can handle single-threaded sequential write+fsync in \~44 microseconds
  * Within the same avilability zone (relatively low network latency environment) one-way network transit time of \~ 0.05 milliseconds between broker and bookie

## Search
* Elasticsearch
  * Sub-1ms search latencies are achievable
  * 3k queries per second with <1 second latency for filter queries

## Networking
* Under typical network conditions, the entire TCP three-way handshake = 1.5 round-trip time (~72-300 milliseconds)
* Even on the same host, TLS negotiation can take 15 ms
* Complete HTTPS connection
  * TCP 3-way: 1.5 RTTs
  * TLS 1.3: 1 RTT
  * Total: 2.5 RTTs
* HTTP keepalives allow clients to reuse SSL sessions
* CDN early termination allows terminating the connection close to the user, significantly reducing the cost of TCP and TLS handshake
