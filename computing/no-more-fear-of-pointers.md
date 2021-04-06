# [No More Fear Of Pointers](https://themightyprogrammer.dev/article/pointer-primer)

* A variable is a memory container where a value resides
  * An address is where a variable resides in memory
  * Each byte is addressed for the processor to access for reading or writing the value
  * A variable may span multiple bytes
    * An `int` variable spans `4 bytes` and has comprises of `4` memory addresses, but always use first-byte address as the first-byte address + type can be used to calculate all the relevant memory addresses
* A pointer is a variable that points to another variable
  * The value of a pointer is a memory address (i.e. the "starting address" of another variable)
* A memory address is a number, so the type of a pointer is always integer despite whom it is pointing at
* Pointers facilitate indirect access to a variable which enables the passing of variables to functions efficiently
* When values are passed to a function, the values are copied to function parameters
  * The copying may cause performance penalties for multi-valued types
  * Instead, pass values via pointers and retrieve the underlying value using the dereference operator (`*`) to access the value which the pointer "points" at
* Can iterate over an array in memory using only pointer arithmetic
  * For example, if there are an array of `int`s, and the first element in the array is at memory address `0`, then the next element in the array will be at memory address `4`
* Pointers can reference functions, which helps in passing functions to functions
* In C++ member methods or fields can be accessed from a pointer like `(*pointer).member` or `pointer -> member`
* A `null` pointer does not point to any memory location / variable
