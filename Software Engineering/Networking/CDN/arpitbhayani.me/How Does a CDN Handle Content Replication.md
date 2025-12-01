# [How Does a CDN Handle Content Replication](https://arpitbhayani.me/blogs/cdn-content-replication)

## Push-Based Replication
* Origin server proactively sends content to CDN edge nodes
* CDN receives upload of new JS bundle
* CDN initiates replication job that creates a DAG of distribution tasks
* If there are 200 edge locations, the CDN doesn't push directly from the origin server to all 200 locations as this may overload your origin server's bandwidth
  * Pushes to 5-10 regional parent nodes (tier-1 caches)
  * Parents distribute to 20-30 mid-tier nodes (tier-2 cachhes)
  * Mid-tier nodes then replicate to edge nodes in their region
* Best for large files that don't change often
  * Content with high demand
  * Time-sensitive content that needs to be everywhere immediately
  * Origin server can support initial bandwidth to tier-1 caches

## Pull-Based Replication
* Content is only replicated to an edge node when a user requests it from that location
* If a user in Mumbai requests `example.com/bundle.js`
  * This requests hits CDN's Mumbai edge node
  * Edge node checks its local cache, leading to a cache miss
  * Edge node then makes a request to its parent node or directly to the origin server
  * Edge node receives content, stores it locally, and serves it to the user
  * Next user in Mumbai gets a cache hit, no origin request needed
* Usually there's at least 3 cache tiers
  * User -> Edge cache (cache miss) -> Regional cache (cache miss) -> Origin shield (cache miss) -> Origin server
  * Origin shield sites in front of origin to prevent stampedes

### Cache stampede Protection
* When content isn't cached and then there are many requests for the same content, don't want each request to cache miss and then result in origin server each

## Data Structure for Replication
* Most CDNs use content-addressed storage
* Instead of storing files by their URL alone, CDNs compute a hash of the content and use the hash as part of the cache key
* Cache Key = Hash(URL + Hash(content))+ Vary headers + Query params)
* When Origin server serves content it includes an `ETag` header
* The CDN edge stores this `ETag` header with the content
  * When checking if the cached content is still valid, the edge makes a request with a `If-None-Match: {previous ETag header}` header value
  * If content has not changed, the origin server responds with a `304 Not Modified` response indicating that no data transfer is needed

## Multi-region replication architecture
* Each regional hub uses Anycast routing, multiple servers share the same IP address, and requests are routed to the nearest/least-loaded server
* CDNS optimize transferring data between nodes by
  * Only transferring diffs between versions
  * Chunk transferring large files in parallel
  * Edge nodes can fetch from nearby edges and not parent nodes (peer-to-peer)
  * Compression algorithms that are optimized for specific content types
