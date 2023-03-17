# [JavaScript's Memory Model](https://medium.com/@ethannam/javascripts-memory-model-7c972cd2c239)

## Variable declarations for JavaScript primitives

* `let myNumber = 23;`
* Unique identifier for variable is created (`myNumber`)
* Allocate address in memory that is assigned at runtime
* Store value at address
* `myNumber` (Identifier) -> `0012CCGWH80` (Address) -> `23` (Value)
* `myNumber` equals the memory address that holds the value `23`
* `let newVar = myNumber` implies that `newVar` would also equal the memory address that holds `23`
* `myNumber = myNumber + 1` results in `myNumber` equaling the memory address that holds `24` but `newVar` will still equal memory address that holds `23`
  * This is because primitive data types in JavaScript are immutable so JavaScript allocates a new address in memory for the new primitive value

## The Call Stack and the Heap

* The call stack stores function calls and primitives
* The heap is where non-primitives are stored
  * The heap can store unordered data that can grow dynamically which is perfect for arrays and objects
* Non-primitives have memory addresses on the call stack but these memory addresses point to memory addresses on the heap, where the actual value is stored


## Variable declarations for JavaScript non-primitives

* `let myArray = [];`
* Unique identifier for variable
* Allocate address in memory (assigned at runtime)
* Store a value of memory address allocated on the heap (assigned at runtime)
* Heap memory address stores the value
* `myArray` -> `0458AFCZX91` (call stack memory address) -> `22VVCX011` (call stack value for address which points to memory address on heap) -> `22VVCX011` (memory address on heap) -> `[]` (value on heap)

## Let vs. Const

* `let` allows you to change memory addresses while `const` does not allow you to change memory addresses
* However, assigning an array to a `const` doesn't mean that the array cannot be `push`ed to, for example
  * The memory of the array isn't changing when it's being pushed to

