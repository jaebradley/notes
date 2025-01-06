# [Inside the Python GIL](https://www.dabeaz.com/python/GIL.pdf)

## CPU-bound serial vs. threaded

* Author has a CPU-bound function doing basic math
* Serial execution is faster than running the function in parallel across two threads - ~1.8x slower than sequential execution
* Disabling one of the CPU cores for the threaded case actually improves the performance of the threaded execution

## Python threads

* Python threads are real operating system (POSIX) threads that are managed by the hsot operating system
* Python threads represent threaded execution of the Python interpreter process
* On thread creation, Python creates a small data structure containing interpreter state (`PyThreadState`)
  * Contains current stack frame, current recursion depth, thread ID, per-thread execution information, < 100 bytes
* A new POSIX thread is started
* Then a C function runs the Python callable function (the `Thread#run` method)

## GIL

* Python interpreter has a global variable that points to the `ThreadState` data of the currently running thread
* Threads hold the GIL when executing
* Threads release the GIL when blocked (like waiting for I/O)
* A form of cooperative multitasking
* The interpreter checks threads every 100 interpreter ticks (by default)
* During this check, the GIL is released and reacquired (mostly in the case of multiple CPU-bound threads)
* Thus, multiple CPU-bound threads are now eligible for execution when the currently executing CPU-bound thread releases the GIL
* These interpreter “ticks” loosely map to interpreter instructions
  * They are not time-based
* Long operations can block everything, and ticks are uninterruptible
* A very common problem encountered with Python thread programming is that threaded programs can not be killed with keyboard interrupts
  * Have to use `kill -9`

## Signal handling

* Signal handlers only run in the main thread
* When a signal arrives, the interpreter runs a “check” after every tick, until the main thread executes
* The interpreter quickly acquires and releases the GIL after every tick until the main thread is scheduled
* Remember: Python does not have a thread scheduler and no notion of thread prioritization
  * All thread scheduling is left to the host operating system
* When the main thread is blocked on an uninterruptible thread-join or lock, the main thread will never get scheduled, and thus the signal handler for something like `Ctrl+C` will never execute
* Since interpreter is attempting to thread-switch after every interpreter tick, the program will now execute slowly

## GIL Implementation

* GIL is implemented as either a POSIX sempahore or a pthreads condition variable
* To acquire the GIL, check if it’s free. If it not free, go to sleep and wait for a signal
* To release the GIL, free it and signal
* Since the operating system’s thread execution priority is used, CPU-bound threads have lower priority than I/O-bound threads

## Going back to original example

* Poor performance of parallel CPU example from beginning is due to GIL thread signaling overhead
* After every 100 ticks, the interpreter locks a mutex, signals on a condition variable/semaphore where another thread is always waiting
* Because another thread is waiting, there is extra pthreads processing and system calls get triggered to deliver the signal
* Multi-core threaded performance is worse because there is GIL acquisition thrashing between the two threads as they get scheduled “simultaneously” on different cores

### Thread Thrashing

* Thread 2 is blocked because Thread 1 acquired the GIL
* Every release of the GIL by Thread 1 signals Thread 2
* Since there are two cores, the operating system schedules Thread 2 but leaves Thread 1 running on the other core
* Since Thread 1 is running, it reacquires the GIL before Thread 2 can acquire the GIL
* Thread 2 wakes up, finds the GIL is in use, and blocks again
* Python and the operating system have two incompatible goals
  * Python only wants to run single-threaded and does not want to handle thread scheduling, leaving it up to the operating system
  * The operating system sees multiple cores and wants to schedules as many processes/threads to take advantage of these cores as possible
* Even a single CPU-bound thread can cause thread acquisition issues for other I/O or CPU bound threads
* Even “higher priority” (from the operating system’s perspective) I/O threads can’t wake up fast enough to acquire the GIL before the CPU-bound thread reacquires it
