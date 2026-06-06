# [Unit and Integration Tests](https://matklad.github.io/2022/07/04/unit-and-integration-tests.html)
* Author uses two attributes when analyzing tests: “purity” and “extent”

## Purity
* Purity comes from the perspective of performance - “how much time would 1000 similar tests take?”
* Author has the following test-related speed ordering where each step adds half-an-order of magnitude to the test’s runtime
  * Single-threaded pure computation
  * Multi-threaded parallel computation
  * Multi-threaded concurrent computation with time-based synchronization and disk access
  * Multi-process computation
  * Distributed computation
* Going up this ladder also adds test flakiness
* Pure tests do little-to-no IO, independent of timing and environment, where possible

## Extent
* Extent is the % of the code that is exercised by the test
* While correlated with performance if a database or network is involved, executing more code does not necessarily mean that the code will run slower
* Author has a very “simple” test from a project that is also very pure
  * However, this test exercises the lexing module, the parser, etc. and so the *extent* of the test is quite high
