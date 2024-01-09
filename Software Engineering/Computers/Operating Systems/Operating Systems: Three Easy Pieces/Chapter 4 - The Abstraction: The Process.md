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

## 4.4 Process States

* In the blocked state, a process has performed some kind of operation that makes it not ready to run until some other event takes place
* For example, a process initiates an I/O request to disk
 * It becomes blocked waiting for the I/O request to resolve
 * Some other process can use the CPU

## 4.5 Data Structures

* OS keeps a process list for all processes that are in a ready state
* Keeps track of running process
* OS must track blocked procsses
* OS must wake the correct process when an I/O event completes
* Register context holds register information for a stopped process
 * When a context switch occurs, these registers need to be restored
