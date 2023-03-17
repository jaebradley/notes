# Go For Ruby Developers

* Specifying `package main` at top of file tells Go compiler that code is executable - every executable program starts with `package main`
* `main` function is entry point for program execution
* Need to `go build <package>` to compile the executable inot an executable binary file and then `go run <executable binary file>` to execute that binary file
* Go is statically-typed but it does "smart" type declaration

```golang
// all equivalent
var a int = 1
var a = 1
a := 1
```

* Type syntax for `map` (`hash`es in Ruby) are `map[<key type>]<value type>`
  * `map[string]int` would be a hashmap that maps strings to ints
  * When accessing a value in a `map`, optional second argument that's returned indicates if the key is present in the map
    * `value, exists := somemap["somekey"] `
    * `exists` will be `true` if it exists and `false` if it doesn't
* Go requires length of array during initialization
* Slices can be initialized without a specific size
  * Can append to array / slices by using built in `append` function
  * Can easily copy array / slice using built in `copy` function
* No while loop in Go - only `for` loops
  * `while sum < 1000` => `for sum < 1000`
  * Infinite loop is `for { // blah blah blah }`
  * Each loop uses `range` like `for key, value := range somemap {}`
    * Or `for index, value := range somearray {}`
* Defer is similar to `ensure` in Ruby
  * Ensures that function call is performed later in program execution, usually for cleanup
* Go has pointers which hold the memory address of a given value
  * Pointers are represented by the `*<type>` operator
  * The `&` operator gives the memory address of a value

```golang
// Initialize pointer variable - a will store memory address of b
var a *int
// Create b variable
b := 12
// Have a equal b's memory address
a = &b
// Using the * operator will get the value associated with the address in memory - in this case, 12
fmt.Println(*a)
// Reassign value associated with memory address stored in a (i.e. b)
*a = 5
// Print b, which should now be 5
fmt.Println(b)
```

* No concept of classes in Go - instead there are `structs` and then functions (which are referred to as methods), which can take instances of `structs`
  * Two cases where these methods should take pointers of the `structs` they work with
    * When the `structs` need to be modified
    * When the `struct` has a large memory footprint passing around the memory address is more efficient than passing around the actual `struct`