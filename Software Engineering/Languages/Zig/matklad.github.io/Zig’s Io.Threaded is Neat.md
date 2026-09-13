# [Zig’s Io.Threaded is Neat](https://matklad.github.io/2026/08/06/neat-io-threaded.html)
* Concurrency is about handling asynchronous, non-deterministic events while parallelism is about using hardware resources to do more at the same time
* Parallelism litmus test: parallelism is deterministic / declarative
  * Implementer describes splitting the problem into independent partitions
  * Implements a function that processes one partition at a time

```zig
array.par_iter()
  .map(|I| I * I)
  .sum()
```

* Concurrency litmus test: concurrency involves cancellation
  * Whenever two asynchronous computations occur at the same time, there is a moment when one computation is aware that the other computation is no longer necessary and must be cancelled
  * In general, it is not possible to just wait for the other computation to complete
  * Often times, the reason that the other computation must actively be cancelled is that the other computation cannot complete because the first computation succeeded
    * i.e. the other computation is waiting for a message that it will never receive
