# [Designing Youtube or Netflix](https://github.com/gitgik/distributed-system-design/blob/master/designing_youtube_or_netflix.md)

## Capacity Estimation

* 1 billion total users
  * 800 million DAU
  * Average of 5 views per day
  * Total views per second is ~46k videos/second
* Far more viewed videos than uploaded videos
  * 200 video views (reads) to 1 video uploaded (writes)
  * 46k viewed videos => ~230 videos uploaded per second
* 500 hours of video are uploaded every minute
  * 1 minute of video needs ~50 MB of storage (multiple formats)
  * Total storage = 500 hours of video per minute x 60 minutes in hour x 50 MB per minute of video = 1500 GB/min or 256 GB/sec
  * Compression and replication would change storage estimates
* Ingress (Upload) bandwidth
  * Assume upload bandwidth of 10 MB/min
  * 500 hours of video per minute x 60 minutes per hour x 10 MB/min = 5 GB/second
* Egress (Outgoing) bandwidth
  * 200 reads to 1 write ratio
  * 5 GB/second (write traffic bandwidth) x 200 (reads to write ratio) = 1 TB/second egress bandwidth

## System API

### Upload Video

* API Key
* Title
* Description
* Tags
* Category
* Language
* Recording metadata
* Video contents
* Returns a successful response, email confirmation

### Search

* API Key
* Search query
* User location
* Videos to return
* Page token / cursor
* Returns response with video data, like title, creation date, views, etc

### Stream

* API Key
* Video ID
* Offset from beginning of video
* Codec and resolution for picking up content that was paused on a different device than the one being used
* Returns a media stream (video chunk) from a given offset

## Components

* Uploaded Video Processing Queue
* Encoding service for encoding uploaded videos into multiple formats
* Thumbnails generator
* Video and asset storage (like thumbnails) in a distributed file storage system
* User database to store name, email, address,e tc
* Video metadata to store information about a video like title, file path, uploading user, views, likes, comments, etc
## Detailed Component Design

* Videos and assets can be stored in a distributed file storage system like HDFS
* Separate read and write traffic
* For asset metadata, use a leader/follower system where writes go to the leader first, and then get applied to followers
  * Followers are temporarily stale when replicating from the leader to the followers

### Thumbnails

* Many more thumbnails vs. videos (assume 5 thumbnails per video)
  * Read-heavy traffic pattern
    * Users may be watching one video, but looking at a page with 20 thumbnails
  * Max of 5 KB in size per thumbnail
* Large number of thumbnail files stored on a disk means many non-sequential disk seeks
* BigTable can be used here
  * Combines multiple files into one block to store on disk
  * Efficient for reading small amounts of data
  * Autoscales
  * Millions of operations per second
* Keep hot thumbnails in a cache
