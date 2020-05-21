# [Elasticsearch From The Top Down](https://www.elastic.co/blog/found-elasticsearch-top-down)

## Node as a Requet Coordinator

* When a request is sent to a node in Elasticsearch, that node becomes the coordinator of the request
* It decides what nodes and shards to route the request to, how to merge different nodes' responses
* The node needs to know the cluster's state - the cluster state is replicated to every node in the cluster
* Needs to know things like the shard routing table (which nodes host which indexes and shards)
* For a search request, coordinating means picking replicas of shards to send requests to for further processing - replicas are picked at random or through the request's preferences
* An index request (i.e where a document is created / updated / deleted) must be routed to one of the primaries of the shard and a certain amount of replicas
* The write consistency of the index request determines how many replicas need to acknowledge receipt of the index request (options are one, a majority, or all)

## Routing

* Documents are routed based on a routing key - documents are placed on shard number `hash (key) mod n` where `n` is the number of shards in the index

## Primary Nodes

* A shard has exactly one primary node and zero or more replicas
* The primary node acts as the coordintaor for index operations for that specific shard
* It sends index operations to the relevant replicas and waits until the required number of replicas have acknowledged before indicating success to the coordinator node
* Being written to the append-only transaction log is what defines success for a shard, not whether the document is actually part of the live index through a searchable segment
* Adding more replicas decreases the overall indexing throughput - need to wait for even more nodes to acknowledge the operations

## Search Requets

* Search requests are routed - a search request will either hit all distinct shards if no routing is specified or a specific shard, if routing is specified
  * Once relevant shards have been identified, coordinator will choose amongst available replicas taking into account load
* A search request also has a `type` - by default  `query_then_fetch`
  * `query_then_fetch` means that there are two rounds of searching
  * First, the shards will each find the top 10 hits and send back their IDs
  * These IDs will be merged by coordinator to find true top 10, after which the winners will have their documents requested
  * For finding global top 10 hits, it is sufficient to ask for top 10 from every shard

## Rewriting Query

```bash
POST /books/book/_search
query:
  filtered:
      filter:
          term:
              tag: python
      query:
          multi_match:
              query: Holy Grail
              fields: [title^5, description]
aggregations:
  author_id:
      terms:
          field: author_id
          size: 10
          shard_size: 100
size: 10
```

* Query describes a search for `Holy Grail` in title and description (preferring a match in title) and returning top ten books and authors
* Elasticsearch needs to rewrite queries to be compatible with Lucene
* While existing DSL are pretty close to Lucene counterparts there are some differences
  * `match` querying has no Lucene counterpart so `match: { query: Holy Grail, fields: [title^5, description] }` would be translated into a `BoolQuery` that matches one of a couple `TermQuery`s like `TermQuery boost=5, title=[holy, grail]` (boost reflects the `title^5` and the search term got tokenized)

## Searching a Shard

* A search happens across multiple independent segments and are merged
* `author_id` field must be in the field cache, if document values are not enabled
  * If not, all `author_id`s for all documents must be loaded into memory
* So for queries and any uncached filters and fields, the inverted index will need to be hit
* Note how the author aggregation will only return an approximation of the top 10 authors
  * Imagine if each shard provided counts for only the top 10 authors per shard - what if one of the "true" top 10 authors was the 11th top 10 author (by count) for that shard?
  * It would not be submitted as a candidate, and that author would not be considered
  * Still possible (which is why it's considered an approximation) if one of true top 10 authors is actually 101st for a given shard, but much less likely
  * To have complete accuracy, ES would have to gather counts for all authors on every shard which would be expensive