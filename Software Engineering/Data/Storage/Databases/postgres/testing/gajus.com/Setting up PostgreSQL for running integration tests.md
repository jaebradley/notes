# [Setting up PostgreSQL for running integration tests](https://gajus.com/blog/setting-up-postgre-sql-for-running-integration-tests)

## What didn't work

* Start with transaction at beginning of test and then roll back at end of test
  * Problematic when writing more complicated integration tests as the behavior would not reflect the real-world behavior of the application in some cases
* Unable to use SQLite as an in-memory stand-in as PostgreSQL-specific functions and features were necessary
* `pg_tmp` ran into performance issues
  * `pg_tmp` creates a temporary PostgreSQL instance for each test
  * `pg_tmp` takes seconds to start and populate a database, and the overhead quickly added up when running thousands of tests

## What worked

* When a database is created from a template database, it has the same schema as the template database
  * Can create copy databases where each database test runs in isolation
  * Takes a few seconds to create these new database from some template database
  * No other sessions can connect to the source database while the template database is being copied
* Mounting a memory disk
  * Create the template database and store it via a temporary file system
  * Data is stored in non-persistent memory, so on server restart, the data is lost
  * Docker container is created with a temporary file system mounted at some path
  * This mounted path is used as the value of the `PGDATA` environment variable, ensuring that PostgreSQL uses the temporary file system for data storage
* The order of operations is to create a template database before running tests, and then before each test, to create a new database from the template database for each test
