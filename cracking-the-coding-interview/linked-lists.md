# Linked Lists

* Represents a sequence of nodes
* These nodes can be "singly" linked so that one node points to the next node
* Or "doubly" linked, where a node points to the next node and the previous node
* Linked lists do not provide constant time access to an element at a particular index in the list
* Linked lists allow adding, removing, and retrieving elements from the beginning of the list in constant time

## Deleting Nodes

* For a singly-linked list, map the previous node's `next` value to the current node's `next` value
* For a doubly-linked list, map the previous node's `next` value to the current node's `next` value (same as singly-linked) but also map the current node's `prev` value to the next node's `prev` value
* Make sure to check for `null` and to update the `head` / `tail` pointer, if necessary

## "Runner" Technique

* Use two pointers to iterate through linked list - one pointer is "fast", often moving multiple nodes at a time, while the other pointer is "slow"
* For example, if you had a linked list of indeterminate size in the form `a1 -> a2 -> ... -> an -> b1 -> b2 -> ... bn` and you want to transform this linked list into `a1 -> b1 -> a2 -> b2 -> ... an -> bn` what you could do is have one pointer move two nodes at a time, and have the other pointer move one node at a time.
  * When the first pointer gets to the end of the list, the second pointer is at the midway point (`b1`)
  * At this point, move the first pointer to the beginning of the list, and start interweaving the values
