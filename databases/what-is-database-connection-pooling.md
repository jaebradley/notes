# [What is database connection pooling](https://stackoverflow.com/questions/4041114/what-is-database-pooling)

* Opening a database connection is expensive
  * Open network sessions, check authentication, etc.
* Pooling keeps connections alive so that when a connection is needed, a connection doesn't need to be created from scratch
* Basic API
  * "Open" - checks the pool for a suitable connection or creates one and gives connection to client
  * "Close" - doesn't actually close connection but puts connection into pool for later use
