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

### Distributed Log Service (like Kafka)
* Published log messages indicate that multipart content has been successfully uploaded
* This video content needs to be transcoded i.e. converted into multiple file types (like different video resolutions)
* We also want the entire video content to be split into 10 second (or so) chunks
* At 1080p, these 10 second chunks are ~3 KB in size. At 4k, these chunks are 50x, or 150 KB.
  * Distributed log consumer can split the large S3 file into byte chunk ranges and publish messages to a downstream topic (`chunk processing topic`) with the source S3 object location and the target byte read range
  * It can also publish some type of checksum object in the expected target processing bucket
    * Imagine an object like `/<video_id>/<md5 hash(all byte chunk ranges, concatenated together)`
    * Basically, only when all listed S3 objects, for each resolution, match the calculated checksum is processing done
      * Note that this implies that the byte chunk ranges need to be part of the transcoded object's name or metadata
  * [S3 supports range queries](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ScanRange.html)
* The `chunk processing topic` consumers reads the bytes from the S3 object and
  * Transcode the bytes across different resolutions
  * There may be other processing in addition to transcoding, like DRM processing
  * Publish the transcoded bytes to an S3 location for "processed" chunks
  * Note that the (`md5`) hash based on the contents of the published S3 object can be part of the published S3 object's name
    * This ensures that the entire upload is deterministic / idempotent - a different set of input bytes would lead to a different published S3 object
    * May want the transcoding + byte range as part of the object prefix to make [listing objects _in-order_ easier](https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-prefixes.html)
  * Could even split this consumer work even further where each message contains the source S3 object location, byte range, and target transcoding resolution
* Can be useful to note that [data transfer within the same AWS region](https://aws.amazon.com/blogs/architecture/overview-of-data-transfer-costs-for-common-architectures/), and especially the same AWS availability zone, is often free
  * Can be lower latency if this data transfer occurs within the same data center
* Can mention that before doing any transcoding, the entire file can be processed and analyzed to find natural frames to chunk instead of literally every 10 seconds
  * For example, chunking after 8 seconds due to a pause in video content
  * This allows the client to have a more seamless experience when starting from a chunk
* When the transcoded files generated by the `chunk processing topic` consumers are published to S3, similar S3-based SQS side-effects can update the video's metadata records with information that the file has been processed
  * Within this side-effect, check if all the listed files match the stored checksum value - if so, can know that processing is complete and that the video metadata record status should be "ready"

## Video Metadata Storage
* Use a durable persistence layer to store the following data
  * Video ID, name, description, and S3 URLs for transcoded files (for different resolutions)
  * Could also store video processing status, like "pending", "processing", "ready"
* Can use DynamoDB, but since we don't expect to add millions of videos per day, the write throughput should be low, so a traditional ACID database could work
  * Similarly, because video metadata will most likely be behind a CDN and most likely won't change after all data has been processed, querying / read throughput will not be in the millions of operations per second range, so a traditional ACID database could also support the expected read patterns
* Video ID is used as the primary key in DynamoDB or a traditional ACID database

## CDN
* Can use Cloudflare or AWS's Cloudfront
  * Can easily set up Cloudfront to read-through to S3 bucket
* [Cloudfront can cache countent based on request headers](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/header-caching.html)
  * Device-specific headers, as Cloudfront introspects the User-Agent
  * The `stale-if-error` header serves stale content even if origin is unreachable
* Cloudflare can also read-through to the S3 bucket
  * [Only allow requests from Cloudflare's IP block](https://developers.cloudflare.com/cloudflare-one/tutorials/s3-buckets/)
  * Can [purge cache by URL prefix](https://developers.cloudflare.com/cache/how-to/purge-cache/purge_by_prefix/), if there is updated content to serve for a given video

## Client Experience
* Client fetches video metadata from CDN via HTTP request
* Video metadata has URL pointing to manifest file, located behind CDN
* Client reads manifest file, and sees all content files, across different formats
* Client chooses format based on network conditions / user settings
* Client downloads first segment and client monitors network conditions / buffered content and may download higher resolution or lower resolution segments (adaptive bitrate streaming)
  * Can stream the content over [Dynamic Adaptive Streaming over HTTP (DASH)](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP)

