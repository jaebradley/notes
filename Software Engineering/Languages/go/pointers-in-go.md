# [Pointers in Go](https://medium.com/rungo/pointers-in-go-a789eafccd53)

* When declaring a variable, Go compiler allocates memory
  * Memory has some address so that Go can get value
  * Memory address is represented as hexadecimal value
* Pointer is a variable that points to a memory location
* A pointer not only saves the memory address of a variable but knows where it is stored in memory and knows how to get the value from memory
* A pointer has data type `*Type` for whatever `Type` a variable has (i.e. `*int` or `*string`)
* The declaration `var pa *int` means that the value of `pa` is `nil`

```go
a := 1
pa := &a
```

* Declare variable `a` and assign it value `1`
* Get pointer for `a` by using `&` operator
* To find a pointer's value use `*` operator (i.e. dereferencing operator)
* To change value at a memory location instead of assigning new value to the variable do `*pa = 2`
