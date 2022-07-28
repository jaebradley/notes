# 3.1 Process Concept

## 3.1.1 The Process

* A process is a program in execution
  * Is comprised of a program's code
  * Current activity (represented by the value of the program counter and the contents of the processor's registers)
    * Program counter indicates the address of the next instruction to be executed for this process
  * The process stack (contains data for function parameters, return addresses, local variables)
  * Heap, which is memory that is dynamically allocated during process run time

```
Stack Memory
     |
     |
     v




     ^
     |
     |
Heap Memory

Data / Global Variables

Text / Code
```

## 3.1.3 Process Control Block

* Each process is represented in the operating system by a process control block, which contains data assocaited with a specific process
* Process state - New, Ready, Running, Waiting, Halted, etc
* Program counter - address of the next instruction to be executed by the process
* CPU registers
* CPU-scheduling information
* Memory management information - page tables
* Accounting information - amount of CPU and real time used, process numbers
* I/O Status information - list of I/O devices allocated to the process, a list of open files, etc
