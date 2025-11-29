# [PostgreSQL Logical Replication](https://eradman.com/posts/postgresql-native-logical.html)
* Based on the write ahead log
* User needs a `replication` attribute
* Set `wal_level` to `logical`
* To select table for export, issue a `CREATE PUBLICATION` command like `CREATE PUBLICATION archivedb FOR TABLE farmer, product;`
* Create a subscription via the `CREATE SUBSCRIPTION` command
  * `CREATE SUBSCRIPTION localharvest CONNECTION 'host=db1 dbname=localharvest user=localharvest password=somepassword' PUBLICATION archivedb WITH (slot_name=archivedb)`
  * Tables must have a primary key
  * Subscriptions can only be created by connecting to a primary
* If a column is added in one database, replication will be blocked but Postgres will retry until the schema change has been applied to all of the subscribers
