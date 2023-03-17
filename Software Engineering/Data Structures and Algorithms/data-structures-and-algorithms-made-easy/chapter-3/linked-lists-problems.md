# Linked List Problems

## Check whether linked list is NULL-terminated or cyclic

* Floyd method of using two pointers
* One point travels one node at a time while the other travels two nodes at a time
* If they ever end up on the same node, then it's a cycle

## If linked list is cyclic, find starting point

* Same Floyd method
* But if / when node meet, to find start of cycle, start another pointer at beginning of list
* Moving one node at a time for both pointers (one at meeting point in cycle and other at beginning of list), start of cycle will be when they meet
* This is because if `x` is length of list before cycle, and `y` is length of cycle until they meet, and `z` is length of cycle from `y` to start
* Then the first pointer has traveled effectively `x + y + z + y` in the same time that the second pointer has traveled `x + y`
* This also means that twice the distance of the second pointer would be equivalent to the distance that the first pointer covered (since it was traveling at twice the rate)
* so `x + y + z + y = 2x + 2y => x = z`

## Reverse a singly linked list

* Recursive
  * Reverse of `NULL` is `NULL`
  * Reverse of one-element list is element itself
  * Reverse of two-element list is second element followed by first
    * Extending this, it's whatever the result of applying the recursive function to the second element followed by the first element

## Two singly linked lists intersect - find intersection point

* Using stacks
  * Push one list into one stack and the other into another stack
    * So now last nodes from each list are at top of each stack
  * Pop off nodes from the stack until the nodes at the top of the stack are not equal
    * The previous node is the merge node / point of the lists

## Display a linked list from the end

* Use call stack and traverse linked list recursively
* On the way back, start printing elements

```javascript
function print(node) {
  if (!node) {
    return;
  }

  // will keep calling this / adding to call stack until end of list
  print(node.next);

  // As call stack is popped off, these console logs get executed
  console.log('this node is', node);
}
```

## Merge two sorted lists into third list

* Iterate through nodes, one at a time, for each list
* Compare the two nodes, and add the smaller node to the third list
* Keep doing this until both lists are fully traversed

```javascript
// Recursive
function merge(firstNode, secondNode) {
  if (firstNode == null) {
    return secondNode;
  }

  if (secondNode == null) {
    return firstNode;
  }

  if (secondNode.value > firstNode.value) {
    node = firstNode;
    node.next = merge(firstNode.next, secondNode);
  } else {
    node = secondNode;
    node.next = merge(firstNode, secondNode.next);
  }

  return node;
}
```

## Reverse linked list in pairs

* Recursive approach
  * Base case for empty or 1-element list is to simply return the list / keep the list the same
  * If not empty or 1-element list
    * Keep reference to second element
    * Reassign first element's next to second element's next
    * Reassign second element's next to first element
    * Assign first element's next next to recursive call to same function
      * where the element is first element's next next
* Iterative approach
  * `while` loop through list two elements at a time and swap the elements using approach in recursive strategy
