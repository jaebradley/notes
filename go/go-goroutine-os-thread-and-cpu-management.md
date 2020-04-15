# [Go: Goroutine, OS Thread And CPU Management](https://medium.com/a-journey-with-go/go-goroutine-os-thread-and-cpu-management-2f5a5eaf518a)

* Goroutine (`G`), worker thread or machine (`M`), processor / a resource that is required to execute Go code (`P`)
* Each goroutine runs on an OS thread that is assigned to a logical CPU
* Go creates a list of different processors based on the number of CPUs on machine, storing them in a list of idel processors
* When new goroutines are ready to run, they will wake up a processor, which will create a worker thread with an associated OS thread
* A worker thread with no work (i.e. not associated with a goroutine waiting to run) goes to an idle list of worker threads