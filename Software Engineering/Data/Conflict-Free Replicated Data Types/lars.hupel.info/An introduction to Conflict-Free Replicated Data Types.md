# [An introduction to Conflict-Free Replicated Data Types](https://lars.hupel.info/topics/crdt/02-contracts/)

## Example
* Set containing elements 1 and 2, replicated across Alice and Bob's computers
* If Alice and Bob only add elements, then synchronizing the sets means taking the union of the elements
* However, consider the case where Alice loses network connectivity, adds 3, then deletes 3
  * Meanwhile Bob adds 3
  * Not enough metadata to know what the set should contain

 ## Example CRDT
<img width="1806" height="432" alt="Screenshot 2025-09-15 at 07 01 53@2x" src="https://github.com/user-attachments/assets/0411c6bb-c5c8-4c49-998b-fecc9ca18eb5" />

* Grow-Only set: not that useful because sometimes you want to delete things
* But provides examples of interfaces and contracts
