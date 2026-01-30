# [Java JIT Compiler](https://medium.com/runtimeerror/java-jit-compiler-c538e5e06a2)

* A virtual machine is a program that allows running a program (i.e. something that can be transformed into Java byte code) on this virtual machine then directly on the operating system itself
  * Running on operating system directly is called "native code" and running on a virtual machine is called "byte code"
  * The `javac` compiler generates byte code and can be run on the Java virtual machine
* When running Java code, the interpreter translates the byte code line by line and executes those lines
  * This takes much more time than C code, which after compilation into machine code, can be run directly by the OS with no time-cost for translation
  * The JIT compiler tries to remove some of this time cost by translating re-occuring byte code into machine code
    * When it sees certain byte code, the interpreter will have the machine code in a cache that it can reference

## Components of the JIT Compiler

* It has a profiler that maintains a counter of calls to a particular method and when the calls to a particular method pass some threshold value, the JIT compiler compiles that method into native code so that the interpreter can use that native code next time
* There is a memory area called the code cache where the JIT comiler moves methods into the code cache so that the interpreter can access that particular code block quickly
