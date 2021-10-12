[Understanding Allocations in Go](https://medium.com/eureka-engineering/understanding-allocations-in-go-stack-heap-memory-9a2631b5035d)

* In many popular programming environments, the stack usually refers to the call stack of a thread
  * Call stack is a last-in-first-out data structure that stores arguments, local variables, and other data as a thread executes functions
  * Each function call adds a new frame to the stack and each returning function removes from the stack
* We must be able to safely free memory of the most recent stack frame when it's popped
  * Thus, we can't store anything on the stack that needs to be referenced outside the context of the the stack
* Since threads are managed by the OS, the amount of memory per thread stack is typically fixed (8MG in many Linux environments)
  * Need to be mindful of how much data ends up on the stack, especially with recursive functions
* Goroutine are very similar to threads, but exist within the Go runtime, so the Go runtime sets the rules of how the Goroutine stacks behave (for example, Goroutine stacks start with 2KB of memory)
  * Before each function call, a check within the function prologue is executed to verify that a stack overflow won't occur
  * If a stack overflow will occur, the runtime would copy the current stack of a new larger space of contiguous memory before executing the function
  * This implies that stacks in Go are dynamically sized and can typically keep growing as long as there's enough memory available to feed them

```golang
func main2() {
   _ = stackIt2()
}
//go:noinline
func stackIt2() *int {
   y := 2
   res := y * 2
   return &res
}
```

* The previous code results in a heap allocation (`res` pointer is moved to the heap)
* `stackIt2` passes the address of `y` up the stack to `main` where `y` is referenced after the stack frame of `stackIt2` has been freed
* The compiler is able to judge that `y` must be moved to the heap to remain alive


```golang
func main3() {
   y := 2
   _ = stackIt3(&y) // pass y down the stack as a pointer
}

//go:noinline
func stackIt3(y *int) int {
   res := *y * 2
   return res
}
```

* The previous code results in `0` heap allocations
* `stackIt3` passes `y` down the stack and `y` isn't referenced outside of `main3` so the compiler can judge that `y` can exist within the stack alone and doesn't need to be allocated to the heap
* Sharing pointers up the stack results in allocations whereas sharing pointers down the stack doesn't (generally)
