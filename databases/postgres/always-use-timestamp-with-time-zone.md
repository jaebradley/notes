# [Always Use TIMESTAMP WITH TIME ZONE](https://justatheory.com/2012/04/postgres-use-timestamptz/)

* Set `timezone = 'UTC'` in `postgresql.conf` - makes `UTC` the default time zone for all connections
* Use `timestamp with time zone` (`timestamptz`) and `time with time zone` (`timetz`) - they store values as `UTC` but convert on selection to whatever your time zone setting is
* Avoid `timestamp without time zone` (`timestamp`) and `time without time zone` (`time`) - they do not know the time zone of a value so different apps can insert values in different zones
* Always specify a time zone when inserting into `timestamptz` / `timetz` column and if time is `UTC` better to add `Z` and be explicit
* If need `timestamptz` in a zone other than UTC use the `AT TIME ZONE` expression - returned value is a `timestamp` with no more time zone