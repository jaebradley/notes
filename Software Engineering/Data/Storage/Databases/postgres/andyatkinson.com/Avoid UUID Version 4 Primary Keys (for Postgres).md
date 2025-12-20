# [Avoid UUID Version 4 Primary Keys (for Postgres)](https://andyatkinson.com/avoid-uuid-version-4-primary-keys)
* UUIDv4 are mostly random values
* UUIDv7 includes a timestamp in the first 48 bits, which works much better with database indices
* One misconception about UUIDs is that they're secure, however, the RFC states that "do not assume that UUIDs are hard to guess; they should not be used as security capabilities"
* Can create short alphanumeric pseudo-random identifiers using integers
  * `2` -> `00000000 00000000 00000000 00000010` (32-bit integer)
  * Using some key, `XOR` the bits
  * Encode the bits using a base-62 alphabet
  * This identifier is stored in a column
* UUIDs are 16 bytes (128 bits) each, which can lead to a lot of extra space when tables have millions of rows
* UUIDv4 primary keys are not ordered so unlike auto-incrementing integer primary keys, inserts are not appended to the right-most leaf page in a B-tree
  * They will instead be inserted into a random page (as they are randomly generated)
  * This could be mid-page or even in an already-full page, causing a page split that would have been unnecessary with an integer
  * Unnecessary splits and rebalancing add to disk space consumption and processing latency for write operations
  * This extra I/O impacts write-ahead log generation
* UUID indices are ~40% larger than bigint so individual lookups, range scans will incur ~40% more I/O on UUID indices as more pages need to be scanned
  * To access even one row, the row's whole page is accessed and copied into a shared memory buffer
* Leaf pages with an id index had an average fill percentage of 98%, UUIDv7 was at 90%, while UUIDv4 was at 79%
* Since UUIDs are randomly located, additional buffers will need to be copied into Postgres' buffer cache compared to ordered integers

## From [the HackerNews discussion](https://news.ycombinator.com/item?id=46272487)
* Permanent identifiers should not carry data
  * Example given is Norwegian IDs have birth date encoded into the ID
    * But then people that don't know when their birth day is are assigned January 1st
    * Then run into issues generating IDs for people with actual January 1st birthdays
  * Another example is that Italian IDs have birth sex encoded into the identifier, which can be changed surgically
