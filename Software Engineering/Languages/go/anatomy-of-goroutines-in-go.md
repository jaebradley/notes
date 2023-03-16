# [Anatomy Of Goroutines in Go](https://medium.com/rungo/anatomy-of-goroutines-in-go-concurrency-in-go-a4cb9272ff88)

```golang
func printHello() {
  fmt.Println("Hello World!")
}

func main() {
  fmt.Println("main execution started")

  go printHello()

  fmt.Println("main execution stopped")
}
```

* Every Go application creates at least one goroutine - the main goroutine which is in charge of executing the `main` function
* Goroutines are scheduled cooperatively, so in the above example, the go scheduler will pass control to the `printHello` goroutine when the main goroutine does execute completely
* When the `main` goroutine is done with execution, the program terminates and the scheduler does not get time to schedule `printHello`
* Can for scheduler to schedule `printHello` by blocking the execution in `main` by using `time.Sleep`

```golang
fmt.Println("main execution started")

go printHello()

time.Sleep(10 * time.Millisecond)
fmt.Println("main execution stopped")
```

* `main` goroutine sleeps for 10 milliseconds and won't be scheduled again for another 10 milliseconds
* So `printHello` goroutine is scheduled and executes
* Then `main` goroutine resumes executing where the stack pointer was

```golang
func printHello() {
  time.Sleep(time.Millisecond)
  fmt.Println("Hello World!")
}

func main() {
  fmt.Println("main execution started")

  go printHello()

  time.Sleep(10 * time.Millisecond)
  fmt.Println("main execution stopped")
}
```

* Only non-sleeping goroutines are considered for scheduling so if `printHello` has a `sleep`, it will tell the scheduler to schedule another goroutine, but since there isn't any avaialble (since `main` is also asleep) `printHello` will wake up after 1 millisecond and continue execution
* If the `printHello` was slept for 15 milliseconds, the main goroutine would be available for scheduling before the `printHello` goroutine woke up which would terminate the program before the scheduler had time to schedule `printHello` again
