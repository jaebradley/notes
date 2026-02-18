# Netflix System Design Notes

## Introduction
The following is my notes for a system design interview that involves building Netflix. [This YouTube video](https://www.youtube.com/watch?v=IUrQ5_g3XKs) was a very helpful resource.

## Back-of-the-envelope calculations
* 1 hr of 1080p = 2 GB
  * 1 hr of 4k = ~100 GB
* Average video = 2 GB
* 1k videos per day = 2 TB
* 365 days = \~1 PB
* 10 years = 10 PB
* 300 million subscribers in 2026, according to [this report](https://www.demandsage.com/netflix-subscribers/)
* At any given time, if there are 10 million active users with 2x that at peak (20 million active users)
* If each user watches average of 2 videos, then we need to support streaming 40 million videos
* 40 million videos = 80 million GB = 40 PB / day

## Content producer upload process
* Content producers (i.e. movie or show video editors) upload finalized video content
* While they can do this in a browser, a GUI (i.e. desktop application) can also provide a useful interface to abstract away the content chunking and storage processing
* Locally, the video content files are chunked and each chunk is stored in a cloud-based storage solution like Amazon S3
* S3 supports [`Multipart Uploads`](https://docs.aws.amazon.com/AmazonS3/latest/userguide/mpuoverview.html)
  * [Multipart upload limits](https://docs.aws.amazon.com/AmazonS3/latest/userguide/qfacts.html)
    * Max object size: `~50 TB`
    * Max parts: `10 k`
    * Part size range: `5 MB` to `5 GB`
* The upload part sizes can be optimized for downstream processing and don't need to be optimized for client-specific transcoding (for example, splitting the content into 10 second chunks)

## Video Processing

### SQS
* [Enable notifications for the ](https://stackoverflow.com/a/38956824)`s3:ObjectCreated:CompleteMultipartUpload`[ event on the target S3 bucket](https://stackoverflow.com/a/38956824)
  * There are [*not*](https://repost.aws/questions/QUaG8yCHRCRWesKEeH-oeU2A/s3-multipart-upload-event-notifications-for-uploadpart#AN-z61XxtcSbKpfl-u40n7XA)[ notifications for each successfully uploaded part](https://repost.aws/questions/QUaG8yCHRCRWesKEeH-oeU2A/s3-multipart-upload-event-notifications-for-uploadpart#AN-z61XxtcSbKpfl-u40n7XA) in a multipart upload
* There's an [SQS Source Connector](https://docs.confluent.io/kafka-connectors/sqs/current/overview.html) to ingest SQS messages into Apache Kafka

 ### Kafka / Distributed Log Service
 * Published log messages indicate that multipart content has been successfully uploaded
 * This video content needs to be transcoded i.e. converted into multiple file types (like different video resolutions)
 * We also want the entire video content to be split into 10 second (or so) chunks

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
