# [Demystifying Dynamic Programming](https://medium.com/free-code-camp/demystifying-dynamic-programming-3efafb8d4296)


* Example problem - given a set of punchcards for an IMG-650 computer, where each punchcard must run at a given start time and stop at a particular time
  * Each punchcard has a value based on how important it is to run for company
  * Need to determine optimal schedule of punchcards that maximizes total value of all punchcards
* Memoization is important for runtime of dynamic programming problems - for example, Fibonacci numbers without memoization takes much longer

```python
def fibonacci(n):
  if n == 0:
    return 0
  
  if n == 1:
    return 1
  
  return fibonacci(n - 1) + fibonacci(n - 2)

def fibonacci(n):
  memo = [0] * [n + 1]
  memo[0], memo[1] = 0, 1
  
  for i in range(2, n + 1):
    memo[i] = memo[i - 1] + memo[i - 2]
  
  return memo[n]
```

* Subproblems for example problem is clear when thinking through how to get maximum value for punchards 1 through n (sorted by start time)
  * Would need to get maximum value for punchcards n - 1 through n
  * Would need to get maximum value for punchcards n - 2 through n
  * ...Would need to get maximum value for punchards 2 through n
* To get recurrrence, think about the decision made at every step
  * If algorithm is at step `i`, what information would it need to decide what to do in step `i + 1` (or if algorithm is at step `i`, what information would it need to decide what to do in step `i - 1`)
* In punchcard case, the decision made at every step is whether or not to run the punchcard
  * The next compatible punchcard is the one with the earliest start time after the current punch card finishes running
  * So the maximum value for punchcards `i` through `n` is the maximum
    1. The value of the current punchard + maximum value schedule for next available punchcard after current one runs
    2. Maximum value schedule for next punch card
  * Once this value is identified for step `i`, memoize it
  
```
OPT(i) = max(value_i + OPT(next[i]), OPT(i + 1))
```

* To solve `OPT(1)` (i.e. the max value of punchcards), start from end and work backwards, and at each step, memoize previous result
  * Base case is starting from `0` value or value of last punchcard
  * By the time, done iterating backwards, the max value should be in the memoization array at index `1`

```python
def punchcard_schedule(n, values, next):
 # Initialize memoization array - Step 4
  memo = [0] * (n+1)
  
 # Set base case
  memo[n] = values[n]
  
 # Build memoization table from n to 1 - Step 2
  for i in range(n-1, 0, -1):
    memo[i] = max(v_i + memo[next[i]], memo[i+1])
 
 # Return solution to original problem OPT(1) - Step 3
  return memo[1]
```
