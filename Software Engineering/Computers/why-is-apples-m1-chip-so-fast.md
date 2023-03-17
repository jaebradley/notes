# [Why Is Apple's M1 Chip So Fast?](https://debugger.medium.com/why-is-apples-m1-chip-so-fast-3262b158cba2)

* A CPU at its most basic level is a device with a number of named memory cells called registers, and a number of computational units called arithmetic logic units
  * The ALUs perform things like addition, subtraction, and other basic math operations
* Modern CPUs cannot do operations on numbers that are not in registers - for example, it cannot add two numbers residing in RAM in two different locations
  * Instead, it must put these numbers into separate registers

```bash
load r1, 150
load r2, 200
aadd r1, 32
store r1, 310
```

* Put number at memory location `150` in RAM and put it in register `r1` in the CPU - same thing with address `200` into register `r2`
* Only then can the numbers be added with the `add r1, r2` instruction
* M1 is not a CPU - it's a whole system on a chip (it has a CPU, a GPU, memory, input / output controllers, etc)
* Instead of adding more general-purpose CPU cores, Apple has added more specialized chips

## Unified Memory Architecture

* For a long time, cheap computer systems have had the CPU and GPU integrated into the same chip (same silicon die)
  * Separate areas of this memory got reserved for CPU vs. GPU
  * If CPU had a chunk of data it wanted the GPU to use, it couldn't say, here have some of my memory - the CPU had to explicitly copy the whole chunk of data over the memory area controlled by the GPU
* CPUs want their plate of data served very quickly by the waiter, but are cool with small portion sizes
* GPUs are cool with slow waiters, but want large portion sizes
* Because of the different needs, putting CPUs and GPUs on the same physical chip was not a great idea
* Another problem is that large GPUs produce a lot of heat
* There is no special area reserved for just the CPU or GPU - memory is allocated to both processors and they can both use the same memory, so copying is not needed
* Apple has built their GPU so it's wattage usage is low so that a relatively powerful GPU can be integrated without overheating

## Multi-core or Out-of-Order processors?

* Add more CPU cores - each core works independently and in parallel
* Make each CPU exeute multiple instructions in parallel
* With two cores, a CPU can carry out two separate tasks concurrently
  * These two separate these two separate programs need some bookkeeping, like where in a sequence of instructions the thread is currently at, and each thread may store temporary values which need to be kept separate
  * In principle, a processor can have just one core and run multiple threads - in such a case, it simply halts one thread and stores current progress before switching to another
  * Not much of a performance enhancement unless the thread has to halt to wait for input from the user or I/O (like data from a slow network connection)
* Desktop software is generally not made to utilize lots of cores - computer games might benefit from 8 cores but probably like 128 cores would be a waste
  * Want fewer cores that are more powerful

## How Out-of-Order Execution Works


* Asking for data in one particular memory location is slow
* The CPU is capable of getting many bytes at the same time
* Getting 1 specific byte in memory takes no less time than getting 100 more bytes following that byte in memory
* Data is sent across a databus - which is like a road or a pipe between memory and fiferent parts of the CPU where data gets pushed through
  * If the databus is wide enough, you can get multiple bytes at the same time
* OOE means that microprocessors are able to analyze a buffer of instructions and see which ones depend on others

```bash
01: mul r1, r2, r3    // r1 ← r2 × r3
02: add r4, r1, 5     // r4 ← r1 + 5
03: add r6, r2, 1     // r6 ← r2 + 1
```

* The second instruction has to wait on the first instruction to complete
* However, the third instruction doesn't depend on calculations from the previous instructions so an OOE processor can begin calculting this instruction in parallel
* The CPU constructs the dependency tree from the set of instructions and to determine which instructions can be performed in parallel
* While instructions may finish out of order, they cannot be committed or made official to the outside world because to the outside world, it has to look as if the instructions were carried out in the same sequence as they were issued

## Why Is AMD And INTEL OOE Inferior to M1?

* It is because the ability to run fast depends on how quickly you can fill up a buffer of micro-operations
* If you got a large buffer then the OOE hardware will have an easier time to locate two or more instructions which it can run in parallel
* No point in having a large instruction buffer if it cannot be refilled fast enough after instructions get picked and executed
* Ability to refill instruction buffer quickly relies on the ability to quickly chop machine code into micro-ops via decoders
* M1 has 8 decoders which is significantly higher than anybody else, so it can fill up the instruction buffer much quicker
* M1 also has an instruction buffer that is 3x larger than what is normal

## Why Can't Intel And AMD Add More Instruction Decoders?

* x86 instructions can be anywhere from 1-15 bytes long while every ARM instruction is 4 bytes long
* Splitting a stream of bytes into instructions to feed into 8 different decoders becomes trivial if every instruction has the same length
* On an x86 CPU, the decoders have no clue where the next instruction starts so it has to analyze each instruction in order to see how long it is
* The brute force way is by simply attempting to decode instructions at every possible starting point
* That means x86 chips have to deal with lots of wrong guesses and mistakes whch has to be discarded
