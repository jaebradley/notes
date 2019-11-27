# [Memory: Stack vs. Heap](https://www.gribblelab.org/CBootCamp/7_Memory_Stack_vs_Heap.html)

## Stack

* Stack is region of computer memory that stores temporary variables created by functions
* Every time a function declares a new variable, it is pushed onto stack
* When function exits, all variables associated with function are freed / deleted - memory is now available for other stack variables
* Stack variables are local in nature - they cannot be accessed outside the function in which they're declared
* There is a limit of the size of variables stored on the stack
* All memory managemement is done by CPU
* CPU organizes stack memory so reading and writing from/to stack variables is very fast

## Heap

* Computer memory that is not managed automatically and is not as tightly managed by CPU (in C)
* Heap does not have size restriction on variables
* Heap memory is slightly slower to read from / write to because uses pointers to access memory
* Heap variables are accessible by any function / global in scope
* In C, pointer is a special data type that store memory addresses for a given value rather than the value itself
* If allocating a large block of memory (like a large array) use heap
* If variable needs to be long-lived use heap
* If variable can be dynamic in size (like an array that may grow / shrink) may be better to use heap
