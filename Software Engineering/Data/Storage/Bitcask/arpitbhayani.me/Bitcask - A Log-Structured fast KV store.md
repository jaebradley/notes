# [Bitcask - A Log-Structured fast KV store](https://arpitbhayani.me/blogs/bitcask)
* Datafiles are append-only log files that hold key/value pairs + metadata
* Bitcask instance can have many datafiles, but only one will be active and opened for writing
  * The inactive log files are considered immutable and only used for reads
* When a datafile is closed, it is never opened again for writing
* The `KeyDir` is an in-memory hash table storing all keys present in the Bitcask instance
  * It maps the offset in the datafile where the relevant log entry resides, facilitating key lookups
  * The value associated with the `KeyDir`  is a structure holding a file ID, offset within that file, as well as metadata
  * The file could be an active or inactive datafile
* When a new key/value pair stored, the key/value pair data is appended to the active datafile
  * Creates a new entry in the `KeyDir` with the offset and file where the new key/value pair is stored
  * Since the datafile is append-only, the disk write does not have to perform any disk seek, which means there is high write throughput
* Updating a key/value pair is similar to adding a new key/value pair
  * Key/value pair value is appended to active datafile 
  * Existing entry in`KeyDir` is updated to point to new file + offset combination
  * Previous entry's file/offset combination is "dangling" and will be garbage collected during merge/compaction
* Deleting a key appends a tombstone value to the active datafile associated with the key
  * Deletes the entry from the in-memory `KeyDir`
  * Similar to updating key/value pairs, there is now a "dangling" key/value entry in a datafile that will need to be garbage collected
* Reading a key/value pair involves finding the datafile + offset from the `KeyDir`
  * Then a disk read occurs to retrieve the value
  * Retrieved value correctness is checked against the checksum stored by the datafile, and then returned to the client
* Merge process iterates over all the immutable (non-active) datafiles
  * Generates a set of datafiles that include only the live and latest versions of each present key
  * Since the key/value data in `KeyDir` exist in a different set of datafiles, the `KeyDir` needs to be updated atomically to reference these new files
* Crash recovery involves reading all the datafiles and rebuilding the `KeyDir`
  * A hint file is created for each datafile
  * This hint file contains all the keys within the datafile
  * Hint file is small in size and helps create the `KeyDir`
* Since the `KeyDir` holds all keys in memory, RAM can be a major constraint
  * Sharded keys (and thus sharded `KeyDir`) can be a solution to scale horizontally
