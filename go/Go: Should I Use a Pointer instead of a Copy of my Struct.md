[Go: Should I Use a Pointer instead of a Copy of my Struct?](https://medium.com/a-journey-with-go/go-should-i-use-a-pointer-instead-of-a-copy-of-my-struct-44b43b104963)

* Using a copy of the struct instead of a pointer is 8 times faster when creating a struct and returning the struct from a function
* When passing a copy of a struct, the heap is not used, and thus there is no garbage collection and no goroutine used for garbage collection
* When passing a struct via a pointer, the go compiler must escape the variable to the heap and put pressure on the garbage collector
* The performance difference is even more stark when setting the processors to `1` via the `GOMAXPROCS` variable
* The performance difference also occurs for tight function calls - in article, example is calling method with a pointer vs. a copy of a struct
