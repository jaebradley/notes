# Implementation

* Each client request results in the creation of a state machine on the node that receives the client request
* Read operation implements the state machine:
  * Send read requests to the nodes
  * Wait for minimum number of required responses
  * If too few replies were received, fail the request
  * Otherwise gather all the data versions and determine the ones to be returned
  * If versioning is enabled, perform syntactic reconciliation and generate an opaque write context that contains the vector clock that subsumes all the reamining versions
* State machine waits for a small period of time to receive any lagging responses from nodes
  * If stales versions were returned in any of the responses, the coordinator updates those nodes with the latest  version (read repair)
* Since each write usually follows a read operation, the coordinator for a write is chosen to be the node that replied fastest to the previous read operation (which is stored in the context information of the request)
