# Circular Linked Lists

* Count nodes by keeping track of start point and then iterating through until node matches the starting node reference
* Inserting at beginning of circular linked list
  * Still need to traverse to end of list to get the tail node
  * Then need to point tail node at newly created node and newly created node points to head
  * Similar procedure for deleting head node
