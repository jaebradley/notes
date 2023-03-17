# [Elasticsearch From The Bottom Up](https://www.elastic.co/blog/found-elasticsearch-from-the-bottom-up)

## Inverted Indexes

* Inverted index maps terms to documents containing the term
* These terms are sorted alphabetically, making it quick to look up a term and the frequency with which it occurs as well as the associated documents that contain that term
* For multiple terms, a simple search is done by looking up each term and what documents they're included in and returning that set of documents (taking the intersection if an `AND` query or the union if an `OR` query)
* In example with simple alphabetically-sorted `term` to `frequency` and `documents` mapping, can efficiently find all terms that start with a `c`
  * Cannot perform an efficient search on everything that contains `ours` because this would require traversing all terms to consider the cause where `ours` is in a substring (like in the case of `yours`)
  * Finding a term by their prefix is `O(log n)` while finding terms by an arbitrary substring is `O(n)`
  * So making things look like term prefixes makes finding things more efficient 
* Example of various ways of taking words and creating term prefixes
  * To find everything ending with a certain string (like `ing`) index the reverse of all terms (so `walking` becomes `gniklaw`) and search for everything starting with `gni`
  * Finding substrings often times means splitting terms into smaller terms called n-grams
    * `yours` can be split into `^yo`, `you`, `our` , `urs`, and `rs$` where `^` and `$` represent the start / end of term
    * To find `ours`, search for `our` and `urs`

## Building Indexes

* The index files Lucene writes are immutable and are never updated except when deleting documents
  * When a document is deleted from an index it is marked as deleted in a special deletion file, which is just a bitmap
  * However, the index themselves are not updated
* Updating a previously indexed document is a delete followed by a reinsertion of the document
  * Updating a document is even more expensive than adding it in the first place
  * Thus, storing rapidly changing data in a Lucene index is not a good idea
* When new documents are added, the index changes are first buffered in-memory and then flushed to disk

## Index Segments

* A Lucene index is made up of one or more immutable index segments
* When processing a search, Lucene does the search on every segment, filters out any deletions, and merges the results across all segments
* As number of segments grows, this process gets more and more tedious
* Lucene will occasionally merge segments according to merge policies as new segments are added
* When segments are merged, documents marked as deleted are finally discarded - this is why adding more documents can actually result in a smaller index size as the act of adding documents can trigger a merge
* As new segments are created, they may cause certain caches to be invalidated, which can negatively impact search performance
  * Caches like the field and filter caches are per segment
* Elasticsearch does continuous index refreshing every second which will cause new segments to be flushed and enable them to be available for seraching
  * Whle indexing documents, Elasticsearch collects them in memory (and in transaction log), writes the segment to disk making the data in the new segment visible for search
  * However, segment has not been `fsync`ed so is still at risk for data loss
  * So when ES "flushes" a segment, it means `fsync`ing the segment and clearing out the transaction log
  * https://stackoverflow.com/a/15429578/5225575

## Elasticsearch Indexes

* An Elasticsearch index is made up of one or more shards which are all individual Lucene indexes
  * Elasticsearch index is made up of many Lucene indexes, which is made up by many index segments
* The number of shards specified at index creation time cannot be changed later on
* As documents are added to the Elasticsearch index, it is routed to a given shard
  * Default behavior is a round-robin based on the has of the document's id
* If the data is time-based like logs, creating an index per time period (like a day) can efficiently limit searches to certain time ranges and make it easier to delete old data
  * Cannot efficiently delete from an existing index but deleting an entire index is cheap
* When searches are limited to a certain user (like searching your messages), it can be useful to route all documents for that user to the same shared to reduce the number of indexes that must be searched

## Transactions

* Elasticsearch has a transaction log where documents to be indexed are appended
* The benefit is that while documents also exist in in-memory buffer, the transaaction log should not be lost on crash, making the set of documents that should be indexed (but haven't been written) durable
* Can also specify consistency level required when indexing - for example, can require every replica to have indexed document before the index operation returns