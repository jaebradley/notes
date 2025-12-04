# [Multi-Master Replication - Why Conflicts Happen](https://arpitbhayani.me/blogs/conflict-detection)
* Shopping cart example where two different writes (i.e. "add product X to shopping cart" and "add product Y to shopping cart") go to two different primary databases
  * Primary database A thinks product X is in the shopping cart
  * Primary database B thinks product Y is in the shopping cart
* Resolution strategy is to use a set union to merge the two shopping carts

## Conflict Detection
* When changes are asynchronously propagated to other primary nodes, conflict is detected
* Easiest way of handling is by making the replication synchronous
