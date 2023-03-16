# [Less Repetition More Dynamic Programming](https://medium.com/basecs/less-repetition-more-dynamic-programming-43d29830a630)

* Divide & Conquer - divides problem into subproblems and then combines subproblems solution to solve larger problem itself
  * Merge Sort - recursively merged subproblem solutions together, while also sorting
* Greedy Algorithm - chooses best option in the moment
  * Makes most efficient choice at each stage, optimistically hoping that if they choose best option at each point, eventually arrive at global optimal choice
  * Another behavior is that they never go back and reconsider prior choices
* DP - divides problems into subproblems, and finds optimal solution for each subproblem, and chooses best option from all subproblems
* DP remembers subproblems using memoization (storing data in some data structure)
* When it sees same subproblem again (an overlapping subproblem) it doesn't need to recalculate work, but rather can access result in data structure
* Fibonacci sequence is good example of DP
  * To calculate next Fibonacci number need previous two Fibonacci numbers
  * So can store two previous numbers in memory, and then reference these to build up to target Fibonacci number
  * Benefit of bottom-up approach is this constant memory footprint
