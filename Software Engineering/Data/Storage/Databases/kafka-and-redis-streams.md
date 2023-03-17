# [Kafka & Redis Streams](https://hackernoon.com/introduction-to-redis-streams-133f1c375cd3)

* We have a very large book that many people read
* People can read this book whenever and however (so can read it fast or slow, and during all hours of the day)
* To keep track of where people are, we maintain a list of the bookmarks that these people set for where they left off
  * Readers can also "give up" and leave their bookmarks in the middle of the book, just taking up space
* This book is being appended to every day, so nobody actually finishes
* Eventually, book is littered with bookmarks and is unaccessible
* Instead of us keeping track of the user's progress using bookmarks, readers should keep track of the last page that they were on
* The downside of having us keeping track of the user's location (i.e. a queue service being responsible for user progress) is that we have to allocate memory per-user
  * Badly-behaved users could request new queue sessions and this could overwhelm the queue service
* Kafka keeps track of events as they come in assigning ids and then each reader keeps track of the last event it has processed
  * Reader will continue to read events from main event server using it's last event id
  * However, since event service does not need to keep track of reader positions, it's memory consumption patterns are more predictable
* Redis trades simplicity for Kafka's distributed resiliency

## Redis Streams

* `XADD <StreamName> * key1 value1 key2 value2 etc` - writes an event into the specified stream with the following key/value properties
* `XREAD COUNT <NumberOfItems> STREAMS <StreamName> <FromItemId>` - reads from specified stream, the specified number of items, from the specified event
