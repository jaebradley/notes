# [Stop Answering the Same Question Twice: Interval-Aware Caching for Druid at Netflix Scale](https://netflixtechblog.com/stop-answering-the-same-question-twice-interval-aware-caching-for-druid-at-netflix-scale-22fadc9b840e)
* Complications with caching for real-time analytics that need rolling-window support
* Time window shifts can lead to a completely different query, resulting in a cache miss
* For most of these real-time analytics queries, the vast majority of data, except for the most recent data, has already settled
* Cache previously seen results, and fetch only the freshest portions from the backing data store
* Given the freshness (and change) associated with recently-arriving data, cache TTLs are set to increase exponentially with the age of the data
  * Data < 2 minutes old gets a minimum TTL of 5 seconds
  * TTL doubles with each additional minute of age
  * Corrections for recently-arriving events are picked up quickly

## Bucketing
* A single-level cache key representing the query and a time interval wouldn’t support caching across time ranges as a shifted window means a different cache key
* Nested maps - top-level map key is the query hash, without any time intervals
  * Inner keys are timestamps bucketed to the queries granularity
  * Timestamps are encoded as big-endian bytes so lexicographic order matches ordering by time
  * Enables efficiently fetching all cached buckets between time A and B
  * A 3-hour query with 1-minute granularity = 180 independent cached buckets
  * Each bucket has its own TTL
  * When the 3-hour query window shifts, most query buckets are reused and Druid is queried for the newest data
* Because bucket TTLs can expire unevenly, when there is a bucket gap (i.e. a minutely bucket is missing), the remaining data is fetched from Druid in a single Druid query
  * Avoids “filling gaps” with multiple small fragmented queries that could increase Druid load
* On a partial cache hit, like querying the most recent 2 hours and 50 minutes of a 3 hour window, the query is rewritten with a smaller interval for the last 10 minutes and then sent to Druid for the much smaller time range
* Cached data and fresh data are combined and returned to the client
* Druid data is also asynchronously written into cache buckets
* When there is sparse data for certain metrics, for example certain time buckets lack data, empty sentinel values are inserted to avoid treating these time buckets as cache expired data and requerying the data from Druid
  * Want to be careful with the most recent data point and make sure it is not being cached with an empty sentinel value when data hasn’t arrived yet
