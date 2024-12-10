# Chapter 7. Compiling to C

* Mathematical Python code tends to run faster when compiled
* Lots of loops that repeat the same operations many times
  * Loops consist of many temporary objects
* Code that makes method calls to external libraries are unlikely to show performance gains after compiling
* Programs that are I/O bound are unlikely to show performance gains after compiling
* Unlikely that Python code compiled to C will run faster than handcrafted C progmra, but unlikely to run much slower

## Why Does Type Information Help the Code Run Faster?

* Python is dynamically typed - a variable can refer to any object of any type, and any line of code can change the type of the object that is referred to
* In example, variable is a float and a complex number at various parts of a program
  * Calling the `abs` function on this variable will work differently for different data types
  * So Python has to look up the type of the variable and determine which "version" of the `abs` function to call
  * This overhead adds up with many repeated calls

## Graphics Processing Units (GPUs)

* Originally designed to help handle the heavy linear algebra requirements of 3D graphics
* Well-suited for solving easily parallelizable problems
* GPUs have slower clock speeds than CPUs
* Have many more compute cores than CPUs
  * CPUs have 12 compute cores while GPUs have thousands
  * AMD Ryzen 7 CPU has 8 cores while NVIDIA RTX 2080 has 4,352 cores

## Performance Considerations of GPUs

* Biggest speed consideration is the transfer time of data from the system memory to the GPU memory
  * Dependent on speed of GPU's bus and the amount of data being transferred
  * Often, training data is too big to fit on the CPU and doing constant data transfers incurs penalties
* There are PyTorch operations like `tensor.numpy` that specifically require an explicit copy _out_ of the GPU
* Using "page-locked" regions of memory, these regions of memory can be copied to the GPU much more quickly
  * Regions can be copied asynchronously

### When to Use GPUs

* Ensure the memory use of the problem will fit within the GPU
* `numpy` functions generally vectorize very well, so if your algorithm can be written in terms of `numpy` calls, your code will probably vectorize well


