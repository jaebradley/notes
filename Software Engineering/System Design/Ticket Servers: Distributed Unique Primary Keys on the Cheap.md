# [Ticket Servers: Distributed Unique Primary Keys on the Cheap](https://code.flickr.net/2010/02/08/ticket-servers-distributed-unique-primary-keys-on-the-cheap/)

* Flickr uses ticket servers to generate unique primary keys
* Flickr shards their data across many databases, distributing load across all the databases
* Flickr sometimes needs to migrate data between databases, so primary keys need to be globally unique

## Why not use UUIDs/GUIDs?

* UUIDs are 128 bits and take up space
* UUIDs have poor database indexing performance and characteristics

## Centralized Auto-Increments

* Use a single database, and use the auto-incrementing ID from that table as the primary key for all databases
* 60+ photos per second, table will grow quickly
  * Does not include comments, favorites, etc, which all need IDs

## `REPLACE INTO`

* `REPLACE` works like `INSERT`, except if an existing row in the table has the same primary key or unique index, the existing row is deleted before the new row is inserted
* Atomically update, in-place, a single row in a database, with a new auto-incremented primary key

## Ticket Server Implementation

* Ticket server is a dedicated database server, with a single database
* Database has tables for 32-bit IDs and 64-bit IDs
* 62-bit schema looks like

```sql
CREATE TABLE `Tickets64` (
  `id` bigint(20) unsigned NOT NULL auto_increment,
  `stub` char(1) NOT NULL default '',
  PRIMARY KEY  (`id`),
  UNIQUE KEY `stub` (`stub`)
) ENGINE=InnoDB
```

* Single row in a ticket table, with an `id` value (that auto-increments), and a "stubbed" meaningless value
* When a new ID value is needed, a `REPLACE INTO` statement is issued, and the updated auto-incrementing `id` value is extracted

## Using Two Ticket Servers

* ID servers are a single point of failure
* DB replication would be problematic with Flickr's write volume
* ID space is distributed across ticket servers by even and odd IDs
  * Each ticket server has an auto-increment set to `2`
  * "Odd" ticket server starts at `1`, and the "even" ticket server starts at `2`
* Round-robin between the two ticket servers to balance traffic load
* Drift (i.e. more evens than odds) is fine, as there is no external or internal impact (other than consistency)
