# [Show some love for prepared statements in Rails 3.1](http://patshaughnessy.net/2011/10/22/show-some-love-for-prepared-statements-in-rails-3-1)

* `ActiveRecord 3.0.0` - `where id = 1` vs. `ActiveRecord 3.1.0` (`where id = ? [["id", 1]]`)
* Idea behind prepared statements is that SQL is compiled and cached for future use
  * This means that the DB server only needs to perform work of compiling the SQL string and build an execution plan once
* Application just needs to apply bind variable (like `id` in the above example)
* `SQLiteAdapter` - cache-service that checks if prepared statement exists and if so, uses it
  * If it doesn't exist, sends SQL to DB to be processed and caches new prepared statement
