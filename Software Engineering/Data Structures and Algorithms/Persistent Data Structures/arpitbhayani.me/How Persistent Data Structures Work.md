# [How Persistent Data Structures Work](https://arpitbhayani.me/blogs/persistent-data-structures-introduction)
* Partially persistent data structures allow access to all historical versions, allowing modifications to only the most-recent version
* Fully persistent data structures allow access and modification to all hsitorical versions and do not restrict any modification whatsoever
  * Modifications to a historical version involve forking history to a new branch
* Confluently persistent data structures allow modifications to historical versions while also allowing them to be merged with existing ones to create a new version from the previous two

## Implementing Partial Persistence
* Naive way to implement partial persistence is to utilize copy-on-write semantics, naively creating a deep copy on every update
  * Inefficient since every write results in the entire data structure being copied
* The "fat node" technique involves the data structure containing a list of versions for the data structure with each version containing a value and a version value
