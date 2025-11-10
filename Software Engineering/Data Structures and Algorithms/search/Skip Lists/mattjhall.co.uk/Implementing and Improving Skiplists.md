# [Implementing and Improving Skiplists](https://mattjhall.co.uk/posts/skiplists.html)
* Each level is a linked list
* Bottom level of the linked list has all the data
* Layer above has half the data and the layer above that has half of the previous layers data, etc
* If data exists on one level, it must be on all the layers below it as well
  * Allows for descending the data structure

<img width="892" height="389" alt="image" src="https://github.com/user-attachments/assets/f617a8a1-4e8d-4d03-92f7-10af831204d0" />

* In the example, search for `200`
  * Start with `3` at the top level
  * Since `200` > `3` move to the next node in the level, `147` 
  * Since `200` > `147` and there is no next node on this level, descend to next level
  * Next level has a next node, `944` , so we know that the target value (if it exists) will be between these two nodes at a lower level
* Continue to descend and find the greatest element less than the target value, until we find the target value or exhaust searching by making it to the lowest (bottom) level
* Insertion works by doing a find / lookup to identify the node on the bottom level that should be before the new value
* Then, insert this value in the bottom level linked list
* Flip a coin - heads means inserting it in the level above. Keep flipping a coin until tails or a new level is created
  * Need to make sure the head (left-most node) is on this new level to correctly use the level
