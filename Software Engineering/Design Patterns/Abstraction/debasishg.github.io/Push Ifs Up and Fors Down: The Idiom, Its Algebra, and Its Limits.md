# [Push Ifs Up and Fors Down: The Idiom, Its Algebra, and Its Limits](https://debasishg.github.io/blog/push-ifs-up-fors-down/)

## Analogy in Database Queries: Projections Early, Joins Late
* In SQL query planning, projections and selections should be performed as early as possible while deferring joins until later
  * Projection is a `SELECT` with specific columns
  * Selections are `WHERE` clauses
* A query plan is a tree whose leaves are table scans who root produces the result
* Since data flows "up" the tree, "down the tree" means "earlier in execution"
  * When a query optimizer talks about pushing a predicate "down" it means evaluating the predicate as early as possible
* Projections reduces the dataset's width while selections act as a filter
* Can process one row at a time, where each operator is called once per tuple
  * Alternative is vectorized / batched execution where each operator is called once per batch of `X` tuples and runs in a tight loop inside the loop

## Analogy in Functional Programming and Category Theory
* Categorically speaking, `Optional<X>` is a `nothing` or an `X`
* So a function that takes an `Optional<X>` and branches inside (like `if(x.isPresent())`) is a pair of functions
* Pushing the `if` "up" factors that pair apart

## [HackerNews Discussion](https://news.ycombinator.com/item?id=49997073)
* ["Never do one of something" / "Batch is the primitive"](https://news.ycombinator.com/item?id=49998706)
