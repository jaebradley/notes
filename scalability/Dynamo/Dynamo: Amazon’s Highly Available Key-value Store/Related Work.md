# Related Work

## Discussion

* Applications that use Dynamo do not require support for hierarchical namespaces or complex relational schema
* Dynamo is built for latency sensitive applications that require 99.9%+ of read and write operations to occur within a few hundred milliseconds
  * Avoid routing requests through multiple nodes
  * Multi-hop routing increases variability in response times
  * Dynamo nodes maintain enough routing information locally to route a request to the appropriate node directly


