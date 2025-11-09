# [Skip Lists in Python](https://www.kuniga.me/blog/2012/09/25/skip-lists-in-python.html)
* Similar to multiple linked lists with some randomization
* First level is a regular linked list with sorted elements
* Each element in this list has some probability `p` to be present in the level above
* Then each of these second level linked list element has some probability `p` to be on the third level, etc

## Search
* To search for an element `q` in the skip list, start at the topmost level of the skiplist
* Iterate over the list until we find a node with the largest element that is smaller than `q`
