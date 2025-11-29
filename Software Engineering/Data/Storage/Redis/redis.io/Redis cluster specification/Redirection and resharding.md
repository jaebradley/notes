# [Redirection and resharding](https://redis.io/docs/latest/operate/oss_and_stack/reference/cluster-spec/#redirection-and-resharding)
* Redis clients can send queries to any node in the cluster
* The receiving node will analyze the query
  * Confirm that only a single key is mentioned in the query or if multiple keys are mentioned, they are all in the same hash slot
  * Will lookup what node is responsible for the hash slot and where the key(s) belong
* If the target hash slot's node is not the same as the receiving node, the receiving node will reply with a `MOVED` error that includes the hash slot and the URL of the instance that can serve the query
* Client needs to reissue the query to the specified node's address

## Live reconfiguration
* Adding or removing a node is abstracted into the same operation, moving hash slots from one node to another
* Adding a new node results in an empty node assigned to the cluster, and a set of hash slots are moved from existing nodes to the new node
* Removing a node reassigns the node's hash slots to the existing nodes
* Rebalancing a cluster results in hash slots moving between nodes
* Example: If hash slot `8` is being moved from `A` to `B`, all nodes will point clients to node `A` for queries involving hash slot `8`
  * All queries about existing keys in `A` are processed by `A`
  * All queries about keys that don't exist in `A` are redirected to `B`
  * This implies that `A` cannot create new keys
* The resultant atomic `MIGRATE` command will connect to the target instance, send a serialized version of the key to migrate, and once an `OK` is received, delete the old key from its own dataset
  * From an external client's standpoint, the key exists in `A` or `B` at any time
  * This will lock both instances for a time
