# [Cache warming: Agility for a stateful service](https://netflixtechblog.com/cache-warming-agility-for-a-stateful-service-2d3b1da82642)

* Strategy for adding a new cache
  * Dual-write to the existing cache and the new cache
  * Allow data to expire in old cache based on TTL value
  * Doesn't work well for clusters that had items with no expiration time
  * Pay extra maintentance cost for clusters that did not experience mutations
