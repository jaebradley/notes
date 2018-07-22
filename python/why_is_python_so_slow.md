# [Why Is Python So Slow](https://medium.com/@anthonypjshaw/why-is-python-so-slow-e5074b6fe55b)

* For multi-threaded programs, need to ensure that when changing variables in memory, multiple threads don't try and access/change the same memory address at the same time
* CPython creates variables, allocating memory
  * Counts the number of references to that variable
  * When references are 0, frees that memory
  * "Temp" variables
* "Global interpreter lock" is used to locks reference count
  * This means that interpreter can only execute one operation at a time, regardless of the thread count
* GIL contention when implementing concurrency via threads when threads were IO intensive
  * Django + WSGI: each request to web-app is separate Python interpreter so only 1 lock per request

## How does JavaScript work?

* JavaScript engines use mark and sweep GC (this is when global objects are traversed and any objects that cannot be reached from globals are eliminated)
* JavaScript is also single-threaded so it doesn't require one - uses event loop + promises / callbacks (Python does something similar with asyncio event loop)

## "It's because it's an interpreted language"

* Python interprets bytecode and executes it locally
* Java compiles to "intermediate language", JVM reads bytecode, and just-in-time compiles to machine code
* JIT compilation requires intermediate language to split code into chunks
* JIT does not make execution faster but enables runtime optimizations
  * JIT sees which parts of applications are being used
  * Java is typed so optimizer can make more assumptions
* JIT's have slow start-up times
* Conversely, Python's dynamic design makes it really hard to optimize
  * Comparing and converting types is costly, every time variable is read, written to, or referenced
