# [A write-ahead log is not a universal part of durability](https://notes.eatonphil.com/2024-07-01-a-write-ahead-log-is-not-a-universal-part-of-durability.html)

## Writing to disk
* A large database represented by a btree that takes up 10 GB on disk
* Writing a single entry to a btree will result in only a (usually) 4 KB page being written
* In the worst case, the entire tree may need to be rewritten
* If the virtual or physical machine this code is running on reboots, the data written to file may not actually be on disk

## fsync
* File data is buffered by the operating system, by default
* Writing data without flushing the operating system buffer is not considered durable
* A commonly accepted requirement for durability is not only that you write data to a file on disk, but that you fsync the written file
* This forces the operating system to flush any data that is buffered, to disk
* fsync is slow and databases offer modes where they do not fsync data before returning a success to a client

## Group commit
* One way to amortize the cost of fsync is to delay requests so that you write data for each of the files and then fsync data for all the requests

## Optimizing durable writes
* It's silly to serialize the entire database to disk every time a user writes
* Write the user's message to an append-only log
* Periodically write the entire btree to disk
* As long as the append-only log has been fsync-ed, we can durably keep the database state without having to write the btree to disk
* Requires that on startup, read the btree from disk and replay the append-only log on top of the btree

## Filesystem and disk bugs
* Sometimes filesystems will write data to the wrong place
* Sometimes disk corrupt data
* Checksum the data on write, store the checksum on disk, and confirm the checksum on read
