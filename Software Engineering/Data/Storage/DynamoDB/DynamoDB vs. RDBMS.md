## [When would you NOT choose DynamoDB where you'd typically use RDBMS?](https://www.reddit.com/r/aws/comments/11bpfen/when_would_you_not_choose_dynamodb_where_youd/)

* Prefer relational databases when query patterns aren't known ahead of time
  * When there are lots of relationships between data, and when update patterns of data would make it cumbersome to maintain denormalized updates
* RDBMS have a much higher cost of ownership in terms of maintaining the database
  * Commenter's company has 8+ year-old DynamoDB tables that don't require maintenance in contrast to 8+ year-old relational databases that lag in having maintenance, especially when there have been outages caused by not handling scheduled maintenance correctly
* Another commenter's approach is to use NoSQL for cache-like data where the data is derived from a main SQL database
  * Always a chance that unknown query patterns are defined for various business logic

## [DynamoDB to Postgres: Why and How](https://news.ycombinator.com/item?id=14605222)

* Primary problem was constantly managing and changing provisioned capacity to respond to usage spikes and minimize costs
* DynamoDB's purpose is to allow you to scale a known query pattern
  * Should not be used as a first solution when you don't know how the data is going to be accessed...or when all the data fits on one Postgres server
  * Commenter has had success starting on a large Postgres server and migrating use cases to DynamoDB when a table gets too big and there is a query pattern that matches a key/value lookup
