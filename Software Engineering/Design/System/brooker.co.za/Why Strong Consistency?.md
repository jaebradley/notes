# [Why Strong Consistency?](https://brooker.co.za/blog/2025/11/18/consistency.html)
* Author ran AWS EC2 control plane using MySQL databases
  * Primary database to handle writes
  * Secondary database to take over from primary database
  * Read replicas to scale reads
    * Other replicas for latency-insenstive reporting
  * Replication via MySQL's statement-based replication
* With read replicas, creating a resource, and then fetching that resource can lead to 404s
  * When the created resource isn't available on read replica that is responding to the fetch / get request
  * Even multiple read attemps for the same resource can have inconsistent behavior with some read attempts succeeding and other read attempts failing due to reads going to different lagging replicas that haven't replicated the resource
* Aurora DSQL has strongly consistent reads
  * Each storage replica gets updates from 1+ journals
  * Writing to journals is monotonic, so once a storage node has seen an update from time `T` it has seen all updates for time < `T`
  * When a query processor starts a transaction, it picks a starting timestamp
    * For every read, it asks the replica "give me the data as of this starting timestamp"
    * If the replica has seen higher timestamps from all journals, then the replica can respond with data
    * If the replica has not seen higher timestamps from all its journals, the replica blocks the read until write streams catch up
