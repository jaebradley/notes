# [Memcached](https://en.wikipedia.org/w/index.php?title=Memcached&oldid=1064747973)

* Effectively a large hash table distrubted across multiple machines
* When the table is full, subsequent inserts cause older data to be purged in LRU order
* Memcached has no internal mechanism to track misses
* Uses client-server architecture
* Servers maintain a key-value associative array
* Clients populate the array and query it by keys (keys <= 250 bytes, values <= 1MB)
* Servers are, by default, on port 11211
* TCP and UDP are supported
* Each client knows all servers, servers do not communicate with each other
* If client needs to set or read value corresponding to a certain key, the client's library first computes a hash of the key to determine which server to use
  * Simple form of sharding
  * The server computes a second hash of the key to determine where to store or read the corresponding value
* Servers keep values in RAM
  * If the server runs out of RAM, it discards the oldest values
* Memcached is a transitory cache - data stored in Memcached may not be there in future
