# Chapter 8. Asynchronous I/O

* Getting the relevant data to your program can be the bottleneck, as opposed to the actual program execution speed
  * Program is I/O bound
* Every time file is read, or network socket is read/written, kernel is needed for interacting with hardware
* I/O operations are orders of magnitude slower than CPU
* So even if kernel is fast, have to wait for kernel to get result from device and return to caller
* Writing to network socket takes ~1ms, which could be millions of instructions on a modern CPU
  * Program is paused for much of this 1ms time (I/O wait) waiting for a signal that the write operation has completed
* Can execute multiple tasks on a single thread that all have I/O wait times concurrently in a way where while one task is waiting another task is executing
* Use mechanism called the event loop to continue performing computation operations and be notified when data is ready to be read

## Introduction to Asynchronous Programming

* When a program enters I/O wait, the excution is paused so that the kernel can perform the low-level operations associated with the I/O request
  * The program is not resumed until the I/O operation is completed
* This context switching is an expensive operation
  * Requires saving the state of the program
  * Loses CPU-level caches
* Event loop manages what runs in program
  * Effetively a list of functions that need to execute, and the function at the top of the list is executed, then next function, etc
  * Works best with asynchronous I/O operations that return immediately
  * When asynchronous operation finishes in the future, an event is fired and program will handle event
*  Generally, event loops take two forms: callbacks or futures

## How Does async/await Work?

* `await` statement is similar to a `yield` statement
  * Execution of the current function gets paused while other code is executed
  * Once the `await` or `yield` resolves with data, the function is resumed
* Event loop is responsible for scheduling the resumption of the paused function

## `tornado`

* Event loop is continuously executing, ideal for any application that is mostly I/O-bound where most of the application should be asynchronous
  * Mostly famous for being a performant web server
* In contrast, `gevent` is an idal solution for mainly CPU-based problems, that sometimes involve heavy I/O
  * Program that performscomputations on a dataset and then send results back to a database for storage

## Shared CPU-I/O Workload

* Example is a CPU-bound problem that needs to communicate frequently with a database to save results
  * Toy problem is `bcrypt` hash generation (with parameters for increasing the CPU and memory requirements of the salt generation)
* For a database used to store simple values, a response time > 10ms should be considered slow

### Batched Results

* Create an `AsyncBatcher` that will queue the result to send to the database in small asynchronous bursts
* Will pause the program and put it into I/O wait to issue many concurrent requests instead of issuing them one at a time
* Start an event loop (`asyncio.get_event_loop`) to run a single async function
* Event loop will run until the asynchronous function is complete

