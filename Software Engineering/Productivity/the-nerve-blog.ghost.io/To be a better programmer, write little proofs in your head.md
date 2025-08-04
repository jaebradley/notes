# [To be a better programmer, write little proofs in your head](https://the-nerve-blog.ghost.io/to-be-a-better-programmer-write-little-proofs-in-your-head/)

## Monotonicity
* Functions that don't go "backwards" (or "forwards")
* Checkpointing / journaling the current pointer to a "step" means that the logic can't re-run a step it's already done
  * Builds logic that should run every step exactly once
* Log-structured merge trees are another example
  * LSM trees keeps a log of all inserts, deletes, updates
  * Scans the log to reconstruct the row value
  * Stale operations are discarded via compaction to save disk space
* "Immutability is monotonicity's cousin"

## Pre- and post-conditions
* A function's pre-conditions are things that are assumed to be true just before the function runs
  * Similarly, a function's post-conditions are things that are assumed to be true just after the function returns
* Pinning down post-conditions is a good way to generate ideas for unit tests

## Invariants
* Invariants of a piece of code are things that should always be true before, during, and after that code runs
* Example of an invariant is double-entry accounting
  * The invariant is that every transaction sees an increase/decrease to credit accounts that must be equal to the decrease/increase to debit accounts
  * Check if debits and credits balance before a transaction and that they balance after the transaction

## Isolation
* A lot of the "craft" of software is centered around modifying or augmenting an existing system without destabilizing anything
* When making modifications to a codebase, it can be useful to know how to prove that behaviors you _didn't_ intend to change were left unchanged
* "When requirements change, extend the behavior of the program by adding new code, not by changing old code that already works"

## Proof-affinity as a quality metric
* You should try and write code in a form that's easy to write little proofs about
