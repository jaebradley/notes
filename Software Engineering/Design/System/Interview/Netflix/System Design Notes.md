1 hr of 1080p = 2 GB
Average video = 2 GB
1k videos per day = 2 TB
365 days = \~1 PB
10 years = 10 PB
300 M subscribers
10 M DAU -> 2x at peak (20 M DAU)
If each watches average of 2 videos = 40 million vides
40 million videos = 80 million GB = 40 PB / day

* how do S3 notifications work?
  * [SNS topics and SQS queues](https://docs.aws.amazon.com/AmazonS3/latest/userguide/notification-how-to-event-types-and-destinations.html#supported-notification-event-types) can receive events
* Use with Kafka / Apache Pulsar?
  * SQS connector for Kafka/Pulsar
* Better solution of adding chunks by resolution in DynamoDB
* How does S3 chunking work?
  * Range queries to read large S3 file into chunks
  * How to know if all chunks have been processed?
    * 2 hours / 10 second clips = \~700 parts
    * hash(Video ID + start / end range or index) as filename
    * Checksum hash (filenames) and if this checksum matches the processed filenames in each
* How does a long-lived Kafka consumer worker work?
  * Consumer already heartbeats for Kafka
  * Pulsar has nacking and acknowledgement timeout redelivery
  * 10 seconds of 1080p is \~ 5 MB
  * Transcoding 5 MB and then publishing to S3
  * DLQ on transcoding or publication failure
* How to check if all chunks have been processed? How to handle chunker failures?
* Roughly how many DAU does Netflix have?
* How does read-through CDN work? Cloudflare WAF?
  * Cloudfront reads-through
  * Cache headers
    * Device Specific headers, Cloudfront introspects the User-Agent
    * Cache-Control: max-age
    * Stale-if-error: serve stale content even if origin is unreachable
  * Cloudflare can read-through bucket
    * Only allow requests from Cloudflare's CDN
    * Purge cache by prefix
* How would CDN cache invalidation work?
* Streaming protocols - DASH / HLS
* ASG for transcoder / topic consumers
  * ASG EC2
  * Amazon MSK

Client fetches video metadata
Returned data has URL pointing to manifest file
Client reads manifest file
Client chooses format based on network conditions / user settings
Client downloads first segment and client monitors network conditions / buffered content and may download higher resolution or lower resolution segments
AWS ElastiCache: Redis Cluster globally distributed
Multi-part upload: Boto3 API that abstracts away the upload, but there is a multi-part callback with configuration for chunk size. Retry any failed parts - maybe can use a queue for failures?
[Amazon S3 Batch Operations](https://docs.aws.amazon.com/AmazonS3/latest/userguide/batch-ops.html) + AWS Lambda, output should be deterministic
