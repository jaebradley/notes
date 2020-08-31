# [FIve Things That Make Go Fast](https://dave.cheney.net/2014/06/07/five-things-that-make-go-fast)

## Memory Footprint

```golang
var gocon int32 = 2014
```

```python
from sys import getsizeof
gocon = 2014
getsizeof(gocon)
# 24
```

* `gocon` will consume exactly four bytes of memory
* In python, the equivalent declaration consumes 6x more memory
* In Java, an `Integer` consumes between 16 bits to 24 bits of memory (depending if it's on a 32-bit or 64-bit JVM)

## Function calls

* When a function is called a new stack frame is created, and the detais of the caller are recorded
* Any registers that may be overwritten during the function call are saved to the stack
* The processor computes the address of the function and executes a branch to that new address
* The Go compiler inlines a function by treating the body of the function as if it were part of the caller
  * Inlining only makes sense when the overhead of calling a function is large relative to the work the function does, so only simple functions are candidates for inlining
  * When a package is compiled, any small function that is suitable for inlining is marked and then compiled as usual

## Escape Analysis

* Determines whether any references to a value escape the function in which the value is declared
* If no references escape, the value may be safely stored on the stack
* Values stored on the stack do not need to be allocated or freed

```golang
func Sum() int {
  numbers := make([]int, 100)
  // implementation
}
```

* `numbers` doesn't escape `Sum` so the compiler will store the `100` integers on the stack rather than the heap so there is no need to garbage collect `numbers` - it is freed when `Sum` `return`s

## Goroutines

* In a multiprocessing / time-sharing system, the operating system must constantly switch the attention of the CPU between processes by recording the state of the current process, then restoring the state of another
  * The cost is that the state of all the CPU registers needs to be maintained and restored for the next process
  * Threads share the same memory space, so they are lighter than processes and are faster to create and switch between
* Goroutines are cooperatively scheduled, which means that switches between goroutines only happen at well-defined points, whan an explicit call is made to the Go runtime scheduler
  * The compiler knows the registers that are in use and saves them automatically
  * When a thread is about to block, the Go runtime spins up a new thread to service other Goroutines while the current thread is blocked
  * Thus, there are relatively few operating system threads per Go process, as the Go runtime takes care of assigning runnable Goroutines to free operating system threads

## Process address space

* Traditionally, inside the address space of a process, the heap is at the bottom of memory and grows "upward" while the stack is located at the top of the virtual address space and grows "downwards"
* The operating system usually arranges to place an area of unwritable memory between the stack and the heap to ensure that if they did collide the program would abort
  * This unwritable memory is called the "guard page"
* Threads share the same address space, so each thread has its own stack
* Since it's hard to predict the stack requirements of each thread, a large amount of memory is reserved for each thread's stack along with a guard page
* As the number of threads increases, the amount of available address space is reduced (i.e. the less heap that is available)
* The Go compiler inserts a check as part of every function call to check if there is sufficient stack for the function to run - if not, the runtime allocates more stack space
  * Because this check and additional allocation can occur, the initial stack size can be much smaller
