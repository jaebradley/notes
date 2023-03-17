# [Securing Your GraphQL API from Malicious Queries](https://blog.apollographql.com/securing-your-graphql-api-from-malicious-queries-16130a324a6b)

* Some naive approaches
  * Size limiting (query size in bytes)
    * Easily bypassed using short field names and will also block legitimate queries with long field names
  * Maintain whitelist of queries
    * Somewhat defeats the purpose of GraphQL API which should represent a very flexible system constrained by a very defined whitelist
* Depth limiting
  * Malicious queries often are deeply nested
  * Various tools to help set a maximum depth for incoming queries
* Amount limiting
  * Don't allow the ability to fetch many objects
  * Define a custom input that has a fixed min and max value
  * Use this custom input to prevent queries of more than the max number of objects
* Query cost analysis
  * For expensive queries, can still overwhelm server under the right conditions (i.e. queries that aren't too deep, but are still very expensive)
  * Specify the relative cost of resolving a certain field or type
  * `field: FieldType @cost(complexity: 2)`
  * Can also often use multipliers using `first` or `last`  value
  * To set accurate complexity values, need to analyze queries to client and figure out the most expensive ones
  * Set a maximum complexity count and fail any queries that go over this max
 
