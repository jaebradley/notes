# [How does Netflix manage to show you a movie without interruptions](https://newsletter.techworld-with-milan.com/p/how-does-netflix-manage-to-show-you)
* Critical microservices
  * Authentication service
  * User profile service: stores preferences and viewing history
  * Content service: provides metadata about available shows and movies
  * Recommendation service: analyze viewing patterns and suggest content
  * Search service: find specific titles
* Orchestration infrastructure
  * API Gateway: handles routing, authentication
  * Service Discovery: manages service registration and service health monitoring
  * Load balancing: distributes requests across healthy service instances
  * Circuit breaker: prevents cascading failures when services are degraded
* Cassandra: highly-available NoSQL database that stores user profile information
* EVCache: enhanced version of Memcached
* MySQL: critical transactional data that requires strong consistency
* Amazon S3: stores video content and analytical data

## Pressing Play: The moment of truth
* Client determines device capabilities and network conditions
* Client asks to stream data from the control plane
* Control plane selects optimal Open Connect server based on
  * Geographic proximity
  * Server health metrics
  * Content availability
* Control plane provides signed URLs to access content
* Client establishes connection to the selected Open Connect server
* Video begins streaming using adaptive bitrate protocols

## Open Connect: Netflix's Custom CDN Architecture
* Physical deployment has two tiers
  * Storage OCAs: deployed at internet exchange points
  * Edge OCAs: deployed within ISP networks, caching the most popular content for that region
* The Open Connect Appliances receive new and popular content based on predicted regional popularity
* Since the OCAs are stored at ISP locations, data can be transferred over Ethernet

## Content Encoding and Adaptive Streaming: Technical decisions
* Content is analyzed to determine visual complexity
* Encoding parameters are determined cased on content characteristics
* Different bitrate ladders are created for different types of content
* After encoding, content is packaged for adaptive bitrate streaming
  * Content is segmented into small chunks (2-10 seconds) where each segment is available across multiple quality levels
  * Client is continuously monitoring available bandwidth and buffer status, switching quality levels to maintain smooth playback
 
## The Data Feedback Loop
* Client-side instrumentation captures performance metrics
* Experimentation platform: A/B testing framework to evaluate changes
* Event data flows through distributed messaging system like Kafka
* Analytical data frameworks like Flink and Spark are used for analysis
