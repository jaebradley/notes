# [Getting More Out Of psql](http://www.craigkerstiens.com/2013/02/21/more-out-of-psql/)

* `\pset linestyle unicode` and `\pset border 2` adds lines to output tables
* \pset null X` (where `X` can be whatever you want it to be) will be the value for `NULL`s
  * By default, `NULL`s are blank values
* Can declare aliases for frequently used queries

```bash
\set show_slow_queries 
'SELECT 
  (total_time / 1000 / 60) as total_minutes, 
  (total_time/calls) as average_time, query 
FROM pg_stat_statements 
ORDER BY 1 DESC 
LIMIT 100;'
```

* `:show_slow_queries` in `psql` command line will execute the query
* To check documentation (or see help) can do `\h some_command` like `\h alter table` - this has auto-completion baked into it
* The `\e` command allows you to see the last command / query and opens your editor with the last command
  * Once the editor is terminated, `psql` sources the file and executes the command / query
  * Only the last query is saved, even if the last `psql` line contained several queries
