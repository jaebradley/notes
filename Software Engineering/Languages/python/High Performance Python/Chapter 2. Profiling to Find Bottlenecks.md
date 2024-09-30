# Chapter 2. Profiling to Find Bottlenecks

* The `timeit` module temporarily disables the garbage collector, impacting the speed one would see in the real-world

## Simple Timing Using the Unix `time` Command

* Specifically use `/usr/bin/time` rather than `time` so the system's `time` program is used and not the simpler and less useful version built into the shell
  * If `time --verbose` results in an error, the shell's built-in `time` is probably being used and not the system `time`
* `-p` flag returns `real` (the wall clock / elapsed time), `user` (the time the CPU spent on your task outside of the kernel function), and `sys` (the time spent in kernel-level functions)
* Look for indicators like `Major (requiring I/O) page faults` when running `time`
  * Indicates OS is having to load pages of data from disk because the data no longer resides in RAM

## Using the `cProfile` Module

* `cProfile` is a built-in profiling tool in the standard library
* `CProfile` has an `-s` flag to tell `cProfile` to sort by the cumulative time spent inside each function, to give information about the slowest parts of a section of code

## Using `line_profiler` for Line-by-Line Measurements

* Profiles individual functions on a line-by-line basis to troubleshoot CPU-bound processes
* The `@profile` decorator is used to mark the chosen function, and CPU time and other statistics for each line of the chosen function are recorded
* In the example, it's identified that `n += 1` is expensive
  * Python has to check to see that the `n` object has an `__add__` function (and it didn't, walk up any inherited classes to see if they have the `__add__` function)
  * Then the second argument (`1`) is passed to the `__add__` function and processed

## Using `memory_profiler` to Diagnose Memory Usage

* Generally, it is more efficient to overallocate memory in a process that can be used at leisure, as memory allocation operations are relatively expensive
* Since garbage collection is not instantaneous, objects may be unavailable but still be in the garbage collection pool for some time
* A line of code may not allocate a deterministic amount of memory (as observed from outside the Python program)
