# [Create a TinyURL System](http://blog.gainlo.co/index.php/2016/03/08/system-design-interview-question-create-tinyurl-system/)

* Each long URL has a corresponding shorter alias
  * Immediately think of key-value pair which makes one think of a hash / hash function
  * This hash function makes a URL to a short alias where the URL maps to a unique alias
  * Each alias maps to a unique URL quickly
* If use A-Z, a-z, and 0-9, this is 62 different characters
  * If alias is 7 characters long, can generate 3500 billion URLs
* One strategy is to keep an incrementing counter for each new URL that's inserted
  * Hash this ID to a 7 character-long alias using base-62 system
* Max cost for each entry is 4 bytes x 7 characters + 4 bytes * 2083 characters (max length of URL) so ~8.5 kb
* Storing one million URLs will require 8.5GB of storage
* Using multiple machines
  * Can use a machine to route alias to the right shard - idea is that the mapping of an alias to the database it actually has it's information stored could be built to be predictable
  * For example, hash % 1024 could split URLs across 1024 different stores
* Problems
  * How to keep reads and writes consistent?
  * How to reshard data stores
  * How to deal with concurrency across multiple machines (multiple users inserting the same URL)?

