# [Zig’s Io.Threaded is Neat](https://matklad.github.io/2026/08/06/neat-io-threaded.html)

## Concurrency vs. Parallelism
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

## Just Use Threads
* If a non-operating programming language system thread is blocked inside of a kernel system call, most programming language APIs don't give a way to unblock the thread
* Signals delivered to a thread blocked in kernel space wake up the thread and the system call return `EINTR`
* Signals are not a cancellation mechanism as signals are inherently racy
  * A signal might get delivered before the relevant system call starts, or after it finishes
  * A thread may bet interrupted by a signal unrelated to cancellation
* The canceling thread sets a flag in shared memory to request cancelation
  * Signals the cancel in a loop until the cancelation is acknowledged via a different flag in shared memory
  * The cancelee thread receives a `EINTR` from the system call and checks whether it is being cancelled by checking the flag in shared memory
  * The cancelee thread retries the system call or acknowledges the cancellation and begins unwinding

## Prior Art
* Java has similar thread interruption mechanisms, but doesn't support interrupting system calls 
