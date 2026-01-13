# [Best practices for implementing version control in DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/BestPractices_ImplementingVersionControl.html)
* Use optimistic locking to prevent conflicting item version updates
  * DynamoDB supports conditional writes
  * Optimistic locking checks if data has changed before writing
  * Each item includes an identifier (a "version" value) that increments with every update
  * In high-concurrency environments, the likelihood of conflicts increase, potentially causing higher retries and write costs
* To conserve reads, there is [a new parameter](https://aws.amazon.com/blogs/database/handle-conditional-write-errors-in-high-concurrency-scenarios-with-amazon-dynamodb/) that returns the item's state at the moment the condition check failed
