# [How To Invalidate Cached Data In Apollo And Handle Updating Paginated Queries](https://medium.com/@martinseanhunt/how-to-invalidate-cached-data-in-apollo-and-handle-updating-paginated-queries-379e4b9e4698)

* Can read from and write to Apollo cache using `readQuery` and `writeQuery` functions
  * For example, insert newly created item into / filter deleted item out of cache, which would propagate changes instantly
  * Can be problematic if there's concurrency issues or paginated queries (depending on what page a user is on or has cached)
* Can manually delete items from the cache using `cache.data.delete(key)`
  * Default key is `typename` + `id`
  * So theoretically, could loop through cache and delete all necessary paginated queries on update or something and force a refetch when next page is requested
* Drawback is that _all_ matching `typename` entries will be deleted - even those that we don't need to delete from the cache (like a list of starred items)
  * This would cause those queries to also be refetched
* Probably performance concerns since the entire cache needs to be iterated over (and this iteration is probably blocking)
* Mutating local cache directly will probably have some unintended consequences