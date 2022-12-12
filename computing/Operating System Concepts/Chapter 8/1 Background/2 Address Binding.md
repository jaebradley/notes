# 8.1.2 Address Binding

* A program resides on disk as a binary executable file
* To execute the program, the program must be brought into memory and placed within a process
* Processes are stored on an input queue, on disk, waiting to be brought into memory
* Select a process in the input queue, load the process into memory
  * Execute the process, loading the instructions and data from memory 
  * Process terminates, and the memory space is declared available
* Compile time address binding
  * If it is known at compile time where a process will reside in memory, then the generated compiled code can start at this location and extend "up" from there
  * If the location of the process' starting location changes, then it will be necessary to recompile this code
* Load time address binding
  * If it is not known at compoile time where the process will reside in memory, the compiler must generate relocatable code
  * Final binding is delayed until load time and if the starting address changes, the user code needs to be reloaded to incorporate this changed value
* Execution time address binding
  * Most general-purpose operating systems use this type of binding
  * Process moves from one memory segment to another during execution
  * Memory address binding is delayed until runtime
