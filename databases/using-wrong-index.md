# [Using Wrong Index](https://www.depesz.com/2010/04/15/tips-n-tricks-using-wrong-index/)

* Table where there's an ID and a creation timestamp that's basically monotonically increasing
* How to gets posts from the last month
  * `SELECT COUNT(*) FROM posts WHERE creation > now() - '1 month'::INTERVAL`
  * Can optimize using index on `creation` column
  * Can also use trigger-based counts that updates some count after each insert
* What if can't create index or don't want to slowdown writes?
  * Find greatest `id` that has a `creation` value less than a month ago
  * Get all rows with an `id` greater than this `id`

```sql
SELECT COUNT(*)
FROM posts
WHERE i > (
  SELECT MAX(i)
  FROM posts
  WHERE creation < now() - '1 month'::INTERVAL
);
```

* It takes `~22ms` vs. `~750ms` for previous query which doesn't have subquery
* Will still be slower than if there was an index
* Will also be worse than if there was a summary table that was updated via triggers

