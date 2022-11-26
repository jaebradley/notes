# [Always use \[closed, open) intervals](https://getpocket.com/read/3750299213)

* `[a, b)` is a short form for expressing `a <= x < b` or the set of all values from (and including) `a` up to (but excluding) `b`
* Edge cases with `[a, b]` (i.e. closed start and end interval)
  * Hard to represent empty interval
  * Partitions are messy
    * Splitting by 24 hours in a day (in 1-hour intervals)
    * `[0, 1), [1, 2), [2, 3)` (etc)
    * With `[closed, closed]` have to do something like `[0, 0:58:58], [1, 1:59:59]` which arbitrarily forces you to pick a precision (e.g. 1 second)
