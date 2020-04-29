# [Follow These Steps To Solve Any Dynamic Programming Interview Problem](https://medium.com/free-code-camp/follow-these-steps-to-solve-any-dynamic-programming-interview-problem-cc98e508cd0e)

* Example problem: you're given a flat runway with a bunch of spikes in it
  * Runway is represented by a boolean array, which indicates if a particular spot is clear of spikes (true / false)
  * You're given a starting speed `S` that is a non-negative integer and determines how far you will move foward with next jump
  * Every time you land on a spot, you can adjust speed by up to 1 unit before next jump
  * Goal is to safely stop anywhere along the runway (does not need to be end of array)
    * "Stopping" is considered when speed is 0
    * Need to avoid any spikes along the way

## Identify Subproblems

* DP is a method for solving problems by breaking them down into a collection of simpler subproblems, solving each subproblem once, and storing solutions
  * When the same subproblem occurs again, simply look up the previously computed solution, trading computation time for additional memory
* For example problem, given a point on the runway, a speed, and the remaining runway, we can determine the spots were we could jump to next
* Whether we can stop from the current point with the current speed depends only on whether we could stop from the point we choose to go to next
  * And where we choose to go next is basically the same problem ("given a point on the runway, a speed, and the remaining runway...") except now, the remaining runway is smaller

## Identify Problem Variables

* Typically, will have one or two changing parameters
* One way to determine the number of changing parameters is to list examples of several subproblems and compare parameters
* So for example problem, the parameters that could change for every subproblems is the array position and the speed

## Express Recurrence Relation

* For example problem, you can adjust speed by up to 1 before jumping
* This means there are 3 possible next speeds, so 3 array positions to end up in next
* `S, P => S, P + S` (same speed)
* `S, P => S - 1, P + S - 1` (decrease speed)
* `S, P => S + 1, P + S + 1` (increase speed)
* If any of these subproblems / next stops can result in stops then we can also stop from `S, P`
* `canStop(S, P) = canStop(S, P + S) || canStop(S - 1, P + S - 1) || canStop(S + 1, P + S + 1)`

## Identify Base Case

* Base case is a subproblem that doesn't depend on any other subproblems because one of the parameters would become a value that is not possible given constraints of problem
* In example problem, `S` and `P` might not be legal if
  * `P` is not within the bounds of the given runway
  * `P` is on a spike
  * `S` is negative
* So base cases are when `P < 0` or `P >= length of runway`, `runway[P]` is `false` (i.e. on a spike), and when `S <= 0` (which would consist of a stop)

## Talk About Recursive vs. Iterative Implementation

* Stack overflow issues are a deal breaker and a reason why you would not want to have recursion in a production system but should be fine in interview if you mention tradeoffs

## Add memoization

* This is where storing the results of each subproblem occur so that no need to recompute values
* Store function result into memory / cache before every return statement
* Lookup memory for the function result before you start doing any other computation