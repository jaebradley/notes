# [Kafka & Redis Streams](https://medium.com/hackernoon/introduction-to-redis-streams-133f1c375cd3)

## Book Analogy (for queue)

* Very long book that many people read
* People read book at different times and they're at different places in book
* People leave bookmark in the book (distributed all over book)
  * Readers leave bookmark and never come back to it so book fills up with bookmarks
* Clever person decides that readers are not allowed to place bookmark in book, but must write down what page they're on in their diary
  * This is the design of Kafka
    * Event readers keep track of the id in the stream whch they have read up to
  * Readers are not responsible citizens and will often not clean up after themselves
* Alternative design is for queue to keep track of where readers are
  * Means allocating memory per-reader, which can be expensive for badly-behaved readers

