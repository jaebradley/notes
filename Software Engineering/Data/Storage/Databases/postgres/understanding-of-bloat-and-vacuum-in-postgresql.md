# [Understanding of bloat and vacuum in PostgreSQL](https://www.percona.com/blog/2018/08/06/basic-understanding-bloat-vacuum-postgresql-mvcc/)

* PostgreSQL rows / tuples have version numbers which are maintained via hidden columns (`xmax` and `xmin`)
* `xmin` - the transaction id of the inserting transaction for this tuple
  * This value is used to identify which transactions could see the given tuple
  * In other words, no transaction with an id _before_ a tuple's `xmin` would be able to see that tuple
* `xmax` - value is `0` for non-deleted tuples
  * Before a `DELETE` is committed, the `xmax` of a tuple is changed to the transaction id associated with the `DELETE`
  * If a `ROLLBACK` occurs, `xmax` remains at the attempted transaction ID - would need to check commit log to see if `DELETE` transaction was actually committed
* An `UPDATE` in PostgreSQL performs an insert and a delete
  * All tuples being updated (i.e. the "old" values) have been deleted and new tuples with the updated values have been inserted, with the updated values
  * These "old" values will have a non-zero `xmax` value
* A tuple that was deleted but is still taking up space and is no longer referenced by any active transiaction is a "dead" tuple
* `VACUUM` is used to reclaim that space by scanning pages for dead tuples and keeping track of the "free" tuple count on each page
* Generally speaking, `VACUUM` doesn't reclaim space to disk, but rather re-uses space for future inserts
* `VACUUM` itself is a non-blocking operation (though there are `VACUUM` variants that _are_ blocking) and does not cause exclusive locks on tables
