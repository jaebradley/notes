# [Build Your Own Database](https://www.nan.fyi/database)
* Starts with a single file example that contains all the key/value pairs
* When updating or deleting a key/value, need to update the data that comes after the key/value in the file by however many bytes are being added / removed
  * Shifting many bytes around each time a key/value is updated is costly
* Never update or delete existing key/value pairs - only add new records (append-only files)
  * To handle duplicate keys, change the search algorithm to look for the last occurrence of the key instead of the first
* File can get very large over time, since we're always appending
* Searching can get slow as each record in the file would need to be iterated
* As the file is append-only, need some mechanism to periodically "shrink" the file
  * Once the file exceeds a certain size, close the file and create a new one
  * While the new file ingests new data, compact the old file by deleting all of its irrelevant data
    * In this case, "irrelevant" data is a key that has been deleted or contains stale data (has since been updated)
* For every record in the file (database), store that record's offset (the number of bytes form the beginning of the file to the start of the record) in the index
* Need an index for each compacted file as the offset is relative to the start of the file
  * Starting with the most recent segment, look up the key in the index
  * If the key is found, read the record at the offset
  * If the key is not found, move on to the next segment
  * Do this for each segment, until the key is found
* Need to keep the index in sync with the data, so updating, deleting, or adding data will change the index
* Currently the index is a purely in-memory index, so there's some upper-bound number of keys that can be supported
  * Index wouldn't help for range queries. Finding all the records between keys `X` and `Y` would mean iterating over the entire database (all segments)
* If the database was always sorted by key, then range queries would be fast
  * This would also mean that we wouldn't need to store the offset of every record in memory
  * If key `10`'s offset was `36` and we didn't know key `18`'s offset, we could start searching from key `10`'s offset since the we know that key `18` must be *after* key `10`.
* Too slow to sort the data on-disk every time a new record is added
* Sort the data in-memory, then write to disk
  * Add new record to sorted in-memory list
  * When the in-memory list gets to a certain size, write it to disk
  * When reading a record, read the in-memory list first, then read from disk, if necessary
* Main downside of having data in-memory is that it's not persisted, so if the program crashes or the computer shuts down all of the data in the in-memory list is lost
  * Every time a record is added to the in-memory list, it is also written to an append-only file on disk, as a backup
  * This append-only file does not need to be sorted
