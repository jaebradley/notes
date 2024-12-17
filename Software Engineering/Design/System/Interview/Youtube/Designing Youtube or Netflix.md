# [Designing Youtube or Netflix](https://github.com/gitgik/distributed-system-design/blob/master/designing_youtube_or_netflix.md)

## Capacity Estimation

* 1 billion total users
  * 800 million DAU
  * Average of 5 views per day
  * Total views per second is ~46k videos/second
* Far more viewed videos than uploaded videos
  * 200 video views (reads) to 1 video uploaded (writes)
  * 46k viewed videos => ~230 videos uploaded per second
* If each video 
