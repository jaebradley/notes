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
