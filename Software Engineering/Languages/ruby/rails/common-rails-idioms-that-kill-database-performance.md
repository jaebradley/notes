# [Common Rails Idioms That Kill Database Performance](http://blog.honeybadger.io/common-rails-idioms-that-kill-database-performance/)

* In the context of `EXPLAIN ANALYZE`
  * `Plan Rows` - in the worst case, how many rows will the database have to loop through to respond to query
  * `Actual Rows` - when query was executed, how many rows did the database actually loop through
  * If `Plan Rows` is equal to rows in database, that's a full table scan
* `Users.exists?` is more performance than `Users.count > 0`
* Make sure things you `ORDER BY` (like `created_at`) have indices (by default, it's `ASCENDING`)
* Pagination with `limit` and `offset` will scan the `limit` + `offset` number of rows
  * So if I have a `limit` of `10` and an `offset` of `100`, query will scan `110` rows
