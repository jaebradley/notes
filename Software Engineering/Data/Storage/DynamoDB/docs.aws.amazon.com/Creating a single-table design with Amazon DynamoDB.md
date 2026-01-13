# [Creating a single-table design with Amazon DynamoDB](https://aws.amazon.com/blogs/compute/creating-a-single-table-design-with-amazon-dynamodb/)
* Local secondary indexes must be created at the time the table is created and support both eventual and strong consistency
* Global secondary indexes only support eventual consistency

## Example application of racers that run races
* Partition key contains a prefix denoting the type of data being stored (i.e. a race or a racer)
  * The sort key can also be race or racer (when the partition key is the opposite) to support querying races by a racer or racers by their races

<img width="520" height="188" alt="image" src="https://github.com/user-attachments/assets/2cd44264-fb92-4fa3-bde8-6caac6bf89f4" /> 

* To get the live race results for a given race can create a GSI where the sort key is the racer's time (in seconds)

<img width="606" height="188" alt="image" src="https://github.com/user-attachments/assets/fe60ad24-d582-4658-82be-cd428cf392ca" />

* To get the second by second times for a given racer, can store the entire timestamp history as a value in a JSON document
  * However, for large amounts of data (max item size is 400 KB), split the data across multiple items
  * So the sort key would be `race-1#part-1` , `race-1#part-2`, etc for the partition key `racer-1` 
  * Can also compress the JSON (like GZIP) which leads to increase in data stored per row at the cost of computation when compressing and decompressing the item values
