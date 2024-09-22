# Chapter 1. Understanding Performant Python

* Two main properties for CPU - operations per cycle (instructions per cycle) and cycles per second ("clock speed")
* Vectorization occurs when a CPU is provided with multiple pieces of data at a time and is able to operate on all of them at once
  * Single instruction, multiple data
* Hyperthreading presents a virtual second CPU to the host operating system
  * Clever hardware logic tries to interleave two threads of instructions into the execution units on a single CPU
  * Results in gains of up to 30% across a single thread
* Out-of-order execution enables a compiler to spot that some parts of a linear program sequence do not depend on the results of a previous piece of work, and therefore that both pieces of work could occur in any order or at the same time
  * As long as sequential results are presented at the right time, the program continues to execute correctly, even though pieces of work are computed out of their programmed order
  * This enables some instructions to execute when others might be blocked (like when waiting for memory access)
* Global interpreter lock ensures that a Python process can run only one instruction at a time
  * Even though some Python code has access to multiple cores at a time, only one core is running a Python at any given time

## Communication Layers

* Frontside bus is the connection between the RAM and the L1/L2 cache
  * The frontside bus moves data that is ready to be transformed by the CPU into a staging ground
  * Also moves finished calculations out of this staging area
* Since GPUs are considered peripheral devices, getting data into and out of a CPU can be a taxing operation
* Heterogenous computing where computing blocks have both a CPU and a GPU on the frontside bus aim to reduce data transfer costs
* Bus width (how much data can be moved in one transfer) and number of transfers per second (bus frequency) contribute to a bus' speed
* Data moved in one transfer is always sequential
* Large bus width can help with any code that sequentially reads through memory
* Small bus width but higher frequency helps with code that does many random access reads
* Chips that are closer to each other allow for faster transfer speeds
* The number of wires joining chips dictates the "width" of the bus

## Idealized Computing Versus the Python Virtual Machine

* Code example that checks to see if a number is prime
* For loop that sends a single value at a time to the CPU
* Ideally, send multiple pieces of data for the CPU to operate on at the same time
  * CPU can vectorize operations with no additional time cost (i.e. do multiple independent computations at the same time)

```python
# Not valid python code
result = (number / numbers[i:(i + 5)]).is_integer()
```

* The above does division and checking on a set of five values at a time
* The CPU can execute these operations at the same time vs. doing separate calculations for every `i` value
* Python objects are not laid out in the most optimal way in memory since it is a garbage-collected language
  * Memory is allocated and freed when necessary, creating memory fragmentation
* No opportunity to change the layout of a data structure in memory, meaning that one transfer on the bus may not contain all the relevent information for a computation, even though it might have all fit within the bus width
