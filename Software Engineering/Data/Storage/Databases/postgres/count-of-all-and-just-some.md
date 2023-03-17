# [Count of all and just some](https://www.depesz.com/2008/10/08/tips-n-tricks-count-of-all-and-just-some/)

* Imagine a `users` table with an `is_active` non-`null`, `boolean` column
* Get the count of all users and all active users
* Obviously way to do this with two queries but is there a way to do it in one query (with one table scan?)
* Another obvious approach is to group by `is_active` and this would provide two rows of data for `true` and `false` and then you could sum both to get the total count
* Can use fact that `count` only counts non-`null` values
  * Can use [`nullif`](https://www.postgresql.org/docs/current/functions-conditional.html#FUNCTIONS-NULLIF) to change the `false`s into `null`s
  * Then `SELECT COUNT(*) AS ALL, COUNT(NULLIF(is_active, FALSE)) AS active FROM users` would get total count and then count of active users
  * Slighty faster than group by approach (at least not slower)

