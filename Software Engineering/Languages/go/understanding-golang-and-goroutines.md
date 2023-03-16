# Understanding Golang And Goroutines

* On a single core system, only one task can be executed at any given time, even if there are multiple tasks in-progress at any given time
* Parallel processing is for multi-core systems where two tasks are literally executing at the same time
* Goroutines are Golang wrapper on top of threads that are managed by the Go runtime instead of the operating system
* Goroutines are much like threads but consumer fewer resources than OS threads
* Goroutines does not have a 1-1 relationship with threads
* Goroutines enable concurrency as well as parallelism if there are multiple cores
* Can be enabled by adding the `go` keyword in front of a function
  * By adding the `go` keyword, a separate Goroutine is created
  * This adds another Go thread to the application
  * Thread is responsible for executing the function on a separate concurrent thread
* By setting the `GOMAXPROCS` function, this will determine the number of cores that an application can scale to