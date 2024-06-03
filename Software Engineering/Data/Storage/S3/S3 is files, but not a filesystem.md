# [S3 is files, but not a filesystem](https://calpaterson.com/s3.html)

* Unix file API abstracts away concerns like buffering, page cache, fragmentation, permissions, I/O scheduling
  * API doesn't expose the concerns to user
  * It's a narrow interface that handles a large number of concerns - a deep module
* Shallow modules have a relatively large API surface relative to what is handled
* Author thinks that if the interface to a given API is YAML than the interface may be shallow
* ORMs are a leaky abstraction - can't use them without some understanding of SQL
* Author's example of a non-deep system
  * The tragedy of SAP is that the entire organization has to understand it, then SAP needs to be reconciled / integrated with everything you do
  * So "simple" / "deep" systems are helpful in minimizing the cost of interaction between two different businesses
