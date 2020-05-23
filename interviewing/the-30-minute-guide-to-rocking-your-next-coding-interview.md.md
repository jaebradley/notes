# [The 30-Minute Guide To Rocking Your Next Coding Interview](https://medium.com/free-code-camp/coding-interviews-for-dummies-5e048933b82b)

* Pick a programming language for algorithmic coding interviews
  * Pick a language you are extremely familiar with
  * There are some languages that are more suitable than other for coding interviews (then there are some you absolutely wnat to avoid like `C` or `Go` because they lack standard library functions and data structures)
  * Python is probably a good defacto choice because it is succinct and has a huge library of functions and data structures
* Consider asking the following clarifying questions
  * How big is the size of the input?
  * How big is the range of values?
  * What kind of values are there? Negative numbers? Floating point? Empty inputs?
  * Duplicates within the input?
  * Extreme cases?
  * Input stored / data structure? If a dictionary of words is it a list of words or a trie?
* It is common for the interviewer to ask follow-up questions like how to handle problem if input it too large to fit into memory, or if input is a stream
  * Answer is usually a divide-and-conquer approach - perform distributed processing of the data and only read certain chunks of the input from disk into memory, write the output back to disk, and combine them later
* Always valdiate inputs first - check for inputs that are invalid, empty, negative - never assume you're given valid inputs (or clarify with interviewer that all inputs are valid which can save time with input validation)
* Check for off-by-one errors

## Sequence

* Sequence = arrays and strings
* Consider duplicate values - how would they affect the answer
* Be mindful of out-of-bounds errors when interating over a sequence
* Be mindful about slicing or concatenating sequences in code - slicing and concatenating sequences requires `O(n)` time
  * Instead, use start and end indices to demarcate a subarray or substring where possible
* Sometimes you'll have to traverse sequence from the right side to left side rather from left to right
* Cases to consider
  * Empty sequence
  * Sequence with 1 or 2 elements
  * Sequence with repeated elements

## Array

* Is array sorted or partially sorted? If so, some form of binary search should be possible
  * Also means that interviewer is looking for a solution faster than `O(n)`
* Can you sort the array - sometimes sorting the array first may significantly simplify the problem
  * Check to see if order of array elements do not need to be preserved before attempting to sort
* If given a sequence and the interviewer asks for `O(1)` space, use array as hash table or modify array to store information
  * If array has values from `1` to `N` (where `N` is length of the array) negate the value at that index to indicate the presence of that number

## Dynamic Programming

* To optimize space, sometimes you do not have to store the entire DP table in memory - the last two values or last two rows of the matrix will suffice

## Graph

* Common graph representations are adjacency matrix, adjacency list, `HashMap` of `HashMap`s
* Some inputs look like trees but are actually graphs - if graph, need to handle cycles and keep track of visited nodes when traversing
  * Corner cases are empty graph, graph with 1 or 2 nodes, graph with cycles
* When recursively traversing a 2D matrix, ensure next position is within boundary of matrix

```python
# Simple template for DFS

def traverse(matrix):
  rows, cols = len(matrix), len(matrix[0])
  visited = set()
  directions = ((0, 1), (0, -1), (1, 0), (-1, 0))
  def dfs(i, j):
    if (i, j) in visited:
      return
    visited.add((i, j))
    # Traverse neighbors
    for direction in directions:
      next_i, next_j = i + direction[0], j + direction[1]
      if 0 <= next_i < rows and 0 <= next_j < cols: # Check boundary
        # Add any other checking here ^
        dfs(next_i, next_j)
  for i in range(rows):
    for j in range(cols):
      dfs(i, j)
```

## Interval

* Usually given an array of two-element arrays (hence the "interval") where the values represent start and end
* Clarify if something like `[1, 2]` and `[2, 3]` are considered overlapping intervals, and how to handle overlapping
* Common routine is to sort array of intervals by start (or end) values first
* Consider the cases where there's a single interval, where there are non-overlapping intervals, where an interval is totally consumed within another interval, and duplicate intervals

## Linked List

* Like arrays, linked lists are used to represent sequential data - the benefit of linked lists is that insertion and deletion of code from anywhere in the list is `O(1)` whereas in arrays, the elements have to be shifted
* Adding a dummy node at the head and / or tail might help handle edge cases where operations have to be performed at the head or tail
  * Presence of dummy nodes ensures that operations will never have to be executed on the head or tail
  * Dummy nodes remove headache of writing conditional checks to deal with null pointers
  * Just don't forget to remove them at end of operation
* Common two pointer approaches
  * Getting the kth-from-the-last node
    * Have two pointers, where one is `k` nodes ahead of the other
    * When the "fast" pointer reaches the end, the other pointer is at a node that is `k` nodes behind the end
  * Detecting cycles
    * One pointer increments twice as quickly as the other - if the two pointers meet, it means there is a cycle
  * Get middle node
    * One pointer increments twice as quickly as the other
    * When "fast" pointer reaches end of list, the "slow"' pointer will be in the middle

## Recursion

* Recursion implicitly uses a stack
* All recursive approaches can be rewritten iteratively using a stack
* Beware when recursion level goes too deep and causes a stack overflow
* Recursions will never be `O(1)` space because a stack is involved unless there is tail call optimization (which varies by language)

## Anagram

* Can sort both strings and should produce same results (`O(n log n)` time and `O(log n)` space)
* Map each character to a prime number and multiply the mapped number together, anagrams should have the same value (`O(n)` time and `O(1)` space)
* Count frequencies of characters in each string (`O(n)` time and `O(1)` space)

## Palindrome

* Reverse string and should equal itself
* Have two pointers at the start and end of string
  * Move the pointers from the outside in until they meet
  * At any point in time, the characters at both pointers should match

## Tree

* A tree is an undirected and connected acyclic graph
* When you notice that a subtree problem can be used to solve the entire problem, try using recursion
  * Remember to check for the base case, usually where the node is `null`
  * Sometimes the recursive function will need to return two values