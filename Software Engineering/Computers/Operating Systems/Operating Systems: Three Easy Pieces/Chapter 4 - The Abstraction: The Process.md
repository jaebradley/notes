# Chapter 4 - The Abstraction: A Process

* A process is a running program
* OS runs one process, stops it, runs another, etc
  * Illusion that many CPUs exist, when in fact there is one physical CPU

## 4.1 The Abstraction: A Process

* A process is the abstraction provided by the OS for a running program
* A process consists of some amount of memory
  * The memory that the process can address (i.e. its address space)
* Special registers
  * Program counter or instruction pointer - which instruction the program will execute next
  * Stack pointer and associated frame pointer - used to manage stack for function parameters, local variables, return addresses
* Programs access persistent storage devices
  * I/O information includes information like the list of open files associated with a process

## 4.3 Process Creation: A Little More Detail

* OS loads program code from disk into memory (more specifically, the address space of the process)
* Memory must be allocated for the program's stack (for local variables, function parameters, return addresses)
* Memory is allocated for the program's heap
 * Heap is used for dynamically allocating data
