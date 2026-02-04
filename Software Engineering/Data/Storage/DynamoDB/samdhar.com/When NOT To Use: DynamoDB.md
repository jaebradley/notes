# [When NOT To Use: DynamoDB](https://www.samdhar.com/distributed-mind-blog/when-not-to-use-dynamodb)
* DynamoDB is built for key/value lookups and range queries on secondary indices
  * For more involved querying, data needs to be pre-processed
* When a query doesn't match table or index schema, DynamoDB resorts to scanning the entire table, leading to performance degradation and increased costs
* DynamoDB items can only be 400 KB in size
  * Oversized objects can be stored in S3 and then referenced in DynamoDB but increases complexity and latency
* Applications can exceed their provisioned capacity or encounter per-partition limitations
* DynamoDB's partition-based design can cause performance issues when specific partitions are hotspots
  * Caching high-demand records via ElastiCache or DAX reduces direct load on DynamoDB
* To avoid throttling, developers often over-provision read and write capacity, leading to wasted spend during off-peak times
* Using DynamoDB often results in denormalization, where data is intentionally duplicated across multiple tables or records to optimize read performance
  * This is counter to traditional SQL databases where normalization is encouraged to reduce redundancy and ensure data integrity
* Once a data model is set, adapting new access patterns or evolving the data model is time-consuming and complex
