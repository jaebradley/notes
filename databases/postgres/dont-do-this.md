# [Don't Do This](https://wiki.postgresql.org/wiki/Don%27t_Do_This)

* Don't use `psql -W` or `psql --password`
  * Flags will tell Postgres to ask for a password before connecting to the server
  * But if server needs a password it'll prompt for it anyways
* Don't use `NOT IN`
  * If doing `NOT IN (SELECT...)` then use `NOT EXISTS`
  * `NOT IN` behaves unexpectedly for `NULL`
  * `SELECT * FROM foo WHERE col IN (1, null)` will always return `0` rows
    * This is because `col IN (1, null)` returns `TRUE` if `col=1` and `NULL` otherwise
    * `NOT(TRUE)` is `FALSE` but `NOT(NULL)` is still `NULL`
    * Since `NOT col IN (1, NULL)` is the same as `NOT(col IN (1, NULL))` it will never return `TRUE`
  * `NOT IN (SELECT...` is hard to optimize
    * Fast for a small result set but for a large result set it is _very_ slow (`O(N^2)`)
* Don't use upper case table or column names
  * Postgres turns all names (columns, tables, functions) to lower-case unless they're double-quoted
  * `create table Foo()` will create a table called `foo` but `create table Bar()` will create a table called `bar`
  * This query will work `SELECT * FROM FOO`
  * This query will work `SELECT * FROM "Bar"`
  * This query will fail `SELECT * FROM Bar`
* Don't use `BETWEEN`
  * It includes values at the ends of the specified range
  * Problematic when doing something like `BETWEEN '2018-06-01' AND '2018-06-02' as it will include midnight on `2018-06-02`
  * Do `timestamp >= '2018-06-01' AND timestamp < '2018-06-02'`
* Don't use timestamp type without timezone
  * Avoid `timestamp` type and use `timestamptz` instead
  * `timestamptz` records a single moment in time (the number of microseconds since January 1, 2000 in UTC)
  * Inserting values in any timezone will get converted to the respective point in time
  * It correctly does arithmetic with times involving different time zones including timestamps on different sides of daylight savings time
  * `timestamp` just records the date and time that are given (calendar and clock instead of point in time)
  * Don't use `timestamp` to store `UTC` values
  * Don't use `timetz` - it was implemented for `SQL` compliance
* Don't use `current_time` function
  * It returns a `timetz` type
* Don't use `timestamp(0)` or `timestamptz(0)`
  * Both functions round the fractional part vs. truncating
  * This could lead to scenarios where you're storing values in the future depending on rounding
  * Instead use `date_trunc("second", blah)`
* Don't use `char` type
  * Use `text` instead
  * Any string inserted into a `char` field will be padded to the specified length
  * Trailing spaces are removed when converting character values to one of the other string types
  * Space-padding wastes space and actually makes processing slower since it may need to strip whitespace in a variety of cases
  * Don't use it even for values that are exactly X characters long
    * This is because `char(n)` won't reject for values that are too short - it'll just pad them with whitespace
    * So no benefit over using `text` with a `check`
* Don't use `money` type
  * Use `numeric` with a `currency` column might be better
  * It doesn't handle fractions of cents
  * It doesn't store currency information - instead it assumes that all values are in the currency specified by the `lc_monetary` locale setting (if this setting changes, then all money columns will reflect the wrong value)

