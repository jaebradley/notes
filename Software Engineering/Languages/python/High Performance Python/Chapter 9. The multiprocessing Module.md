# Chapter 9. The `multiprocessing` Module

* Python threads are OS-native, and _not_ simulated
  * Bound by Global Interpreter Lock, so only one thread interacts with Python objects at any given time
* By executing many processes at the same time, a number of Python interpreters are run in parallel, each with a private memory space with its own GIL

## Estimating Pi Using Processes and Threads

* No speedup when using more threads due to GIL and the fact that the task is CPU-intensive
  * Although each thread could run on a separate CPU, it will only execute when no other threads are running
* Linear speedup with more processes
  * Maximum speedup when number of processes = number of physical cores even if the number of processes can be larger than the number of physical cores due to Hyper-Threading
  * Each forked process has a private Python interpreter running on a single thread
  * No GIL contention across these forked processes as no objects are shared
* Spawning threads is much more lightweight than forking a process
* Expect a forked process to take on 10-20 MB of RAM
  * Increasing the installed libraries and data, each forked copy may end up using hundreds of MB
  * If the system reverts to using disk swap space after it runs out of RAM, any parallelization will be lost to paging RAM back and forth to disk

## Replacing `multiprocessing` with `Joblib`

* Avoid passing large picked objects to processes
* Pandas DataFrames in a dictionary. Cost of serializing these via the `Pickle` module negated the gains from parallelization. The serial version actually executed faster
* Solution was to store the dictionary in a file
* `Joblib` has a `@memory.cache` decorator that caches function results based on the function’s input arguments to a disk cache
* Disk cache is persisted between Python sessions

## Random Numbers in Parallel Systems

* Random number generators are seeded in their forked process
* The `multiprocessing` module will seed the random number generators in each new process if during the process forking `random` is in the Python namespace
* When using `numpy` random number generation, explicitly set the random number seed. If not, each forked process will generate an identical sequence of random numbers

## Using `numpy`

* `numpy` is creating and manipulating the same object types at very low levels in contiguous blocks of RAM vs. many higher-level Python objects that each require individual management and addressing
* For thread-only work, the threads sahre the same random number generator and they access it in series (if the GIL is active)
* Need to call `seed` per-process to ensure that each of the forked processes generates a unique sequence of random numbers

## Finding Prime Numbers

* Balance between many tiny jobs and fewer larger jobs
* Tiny jobs have the greatest communication overhead
* All CPUs will be utilized efficiently, but the communication pipe will become will become a bottleneck as each job and result is passed through the single communication channel
* Increasing the job size has diminishing returns at a certain point
* Need to align the number of jobs with the number of physical CPUs
* By default `multiprocessing` will see hyperthreads as additional CPUs, when only really the actual number of physical CPUs will be running (for CPU-intensive tasks)

## Queues of Work

* `multiprocessing.Queue` objects are nonpersistent queues that can send any pickleable Python object between processes
* Pickling / unpickling carries a communication overhead
* `Queue` lacks persistence in the face of power loss
* Only one process can consume from the `Queue` at any given time - the `Queue` object synchronizes accesses
* `queue.get` blocks until a task is available
* There are two coordination messages that are passed
* The first is a sentinel message that indicates no more work is available
* The second message is sent by a worker to confirm that it has seen the sentinel message and is tearing itself down
* In some cases, if the ratio of the communication cost to workload is high (large pickled objects) then a serial solution can be faster than using a multiprocess `Queue`-based solution

## Verifying Primes Using Interprocess Communication

* Problem: given small set of numbers, figure out if each number is prime

### Naive Pool Solution

* Uses ` multiprocessing.Pool` with four forked processes
* Divide the range of possible factors into four tuples of subranges and sends these to the `Pool`
  * Take square of big number, divide that into four ranges, to represent the ranges of possible factors

### Less Naive Pool Solution

* Serially check smaller factors, then start a parellelized search if no primes are found

### Using `Manager.Value` as a Flag

* `multiprocessing.Manager()` lets users share higher-level Python objects between processes as managed shared objects
* In this case, uses a byte flag value of `1` when a factor has been found
* The flag is synchronized, so don't want to check it too frequently
  * Checking requires a lock on the shared variable
  * So check every one thousand iterations before exiting the search

## Using Redis as a Flag

* Provides its own locking mechanisms
* Each operation is atomic
* Redis stores everything in RAM and snapshots to disk
* Redis supports replication to a cluster of instances
* Unix domain sockets can achieve around 50% more throughput than the TCP/IP loopback
  * Default behavior of redis-benchmark is to use the TCP/IP loopback
  * Long pipelines decrease the performance benefit of unix domain sockets

## Using `Rawvalue` as a Flag

* `multiprocessing.RawValue` is a thin wrapper around `cyptes` block of bytes
* It lacks synchronization, so it avoids locking, but there are no atomic operations
* Benefit in example is that flag gets switched in one direction ("have I found a factor yet?")
  * When this flag is read, it's just to check if searching can stop
* Thus, because the flag is never reset, there doesn't need to be synchronization
* Things like a shared counter will need synchronization and something like a context manager with `value.get_lock()` will be necessary
  * `Value`'s implicit locking doesn't support atomic operations

## Using `mmap` as a Flag

* The bytes in a shared memory block are not synchronized and they come with very little overhead
* They kind've act like a file, and are a block of memory with a file-like interface
* Have to `seek` to a location and read/write sequentially
