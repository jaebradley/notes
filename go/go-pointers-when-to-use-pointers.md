# [Go Pointers: When To Use Pointers](https://medium.com/@kent.rancourt/go-pointers-when-to-use-pointers-4f29256ddff3)

* Two biggest hazards of working with pointers are accidental nil pointer dereferences and accidental mutation of something you didn't want mutated

```golang
var s *S
fmt.Println(s.Name)
```

* `s` is initialized to `nil` because that is the zero value for any pointer
* `s.Name` is automatically dereferenced so that there's an attempt to access the `Name` property of the `S` struct that `s` points
* A nil pointer dereference will terminate entire program

```golang
func printUpper(s *S) {
  s.Name = strings.ToUpper(s.Name)
  fmt.Println(s.Name)
}

s := &S{
  Name: "foo",
}

printUpper(s)
fmt.Println(s.Name) // Prints FOO
```

* Note how `printUpper` mutates the struct - when you pass a pointer to a function all, you are trusting the function to handle the data that pointer references responsibly

```golang
type S struct {
  T *T
}

type T struct {
  Name string
}

func printUpper(s S) {
  s.T.Name = strings.ToUpper(s.T.Name)
  fmt.Println(s.T.Name)
}
```

* Even though in this case `printUpper` receives a copy of `s`, the `T` field inside `s` is a pointer
* When `s` is copied the inner `T` _pointer_ is copied - however, copy of a pointer still _points_ to the same value as original
* So `printUpper` will still modify inner `T.Name` value
* Go utilizes pass by value semantics for function calls so all arguments to a function are copied at the time the function is invoked
  * If a struct passed to a function occupies many bytes or kilobytes of memory, all of that is copied
  * By constrast, if a pointer to the same struct is passed to a function, when that pointer is copied only 8 bytes are copied
  * This also applies to a function's return values
* There are many cases where passing a struct performs better than passing a pointer
* This is because of heap memory vs. stack memory
  * Oversimplified differentiation is that memory on stack is managed efficiently by CUP while memory on heap is managed by program or garbage collector
* Memories for copies of function arguments are usually allocated on the stack
  * When a function returns, the copy of a struct goes out of scope and CPU reclaims memory
* Pointer to a struct is allocated on the heap because value referenced by pointer doesn't necessarily go out of scope when function returns so shouldn't be on stack where CPU eagerly reclaims memory
* Heap memory comes with additional overhead in Go due to garbage collector, which is expensive to run

## No Need To Return Nil

* Imagine some `GetX` call to a database - the signature of `GetX` is `*X` to handle the `nil` case (i.e. the possibility of _not_ finding a value)
  * However, this might get the caller in trouble in terms of nil pointer dereferencing
* Instead, the signature of `GetX` should be `(X, bool)` where the `bool` represents if the call succeeded
  * `X` is the zero value of the struct if the value is not found
  * Now, no nil pointer dereferencing is possible
* Use pointers when actually trying to mutate a struct, or singletons
* Example is something like a database pool where there are a finite number of (precious) connections so copying some of these resources may lead to problems
* Create exported interface that hides the underlying pointers

```golang
type Cache interface {
  Add(key string, val S)
  Get(key string) (S, bool)
  Clear()
}

type cache struct {

}

func NewCache() Cache {
  // Underlying pointer is hidden
  return &cache{}
}

func (c *cache) Add(key string, val S) {

}

// Rest of interface methods
```