# [The challenges of soft delete](https://atlas9.dev/blog/soft-delete.html)
* Database tables will have a lot of dead data
* Makes restoring from a database dump take longer
* Indexes need to be careful to avoid archived rows
* Need to be careful not to leak any archived data
* Migrations that change default values / backfill need to be backwards compatible with records from many years ago
* One approach is archiving the record in S3
  * Triggered by an event sent to SQS
  * And another service would process that SQS event and persist it to S3
  * More likely to have bugs in application code, which meant archived records were lost and manual cleanup of external resources was necessary
  * Larger infrastructural burden, more complex
  * Archived objects in S3 are not the easiest to query
* Triggers can copy a row to an archive table before it's deleted
  * Archive table can be a generic table that stores JSON blobs
  * How does this work with transaction blocks?
* Postgres's WAL records every change to the database
  * Change data capture (CDC) tools can read the WAL and stream those changes to external systems
  * To support archiving, filter for delete events and write the deleted records to another datastore
  * Debezium is the most well-known tool
    * Connects to Postgres's logical replication slot, reads changes, and publishes these changes to Kafka
  * Postgres retains WAL segments until all replication slots have consumed them
    * If a CDC consumer stops processing, WAL segments will accumulate on the primary
    * Without any mitigation, the primary database will run out of disk space and crash
    * `max_slot_wal_keep_size` in Postgres 13+ limits how much WAL a slot can retain
    * If a slot falls too far behind, Postgres invalidates the slot rather than filling more disk space
    * This protects the primary database but also means that your CDC pipeline loses data and will need to be re-synced from a snapshot
