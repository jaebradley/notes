# [Understanding JVM Architecture](https://medium.com/platform-engineer/understanding-jvm-architecture-22c0ddf09722)

* `JRE` is the Java Runtime Environment and includes the JVM and deployment tools (minimum environment needed for running a Java application)
* `JDK` is the Java Development Kit and is the complete Java development environment (includes both JRE and development tools)
* Java source code is compiled into an intermediate state called `bytecode` (which is stored in `.class` files) using the Java Compiler
  * `bytecode` is then interpreted by the JVM into native machine language, which can be understood by the operating system
  * `bytecode` is a platform-independent state which is portable among any JVM regardless of the underlying OS and hardware architecture
  * Still need to select the correct JVM for hardware and OS (really OS version + processor architecture (e.g. `x86`, `x64`))

## Runtime Data Area

### Method Area

* All JVM threads share access to same Method area, so any logic surrounding the Method area must be thread safe
* Method area stores class level data like
  * Classloader reference
  * Constants, references to methods (called the runtime constant pool)

### Heap Area

* One heap area per JVM
* Information about all objects, (like their instance variables) are stored in the Heap

### Stack Area

* Every JVM thread has a separate runtime stack to store method calls
* Every method call pushes an entry to the top of the runtime stack and each entry is called a "stack frame"
* Each stack frame has a reference to the local variable array, the operand stack, and the runtime constant pool of the class where the method is being executed
  * The [operand stack](https://stackoverflow.com/a/24427197/5225575) seems to be kinda like a set of instructions for the operations within a method
  * The size of local variables and the size of the operand stack can be determined at compile time, so the size of stack frames is fixed
* In a stack trace, each line represents a stack frame
* The local variable array stores information about the number of local variables used by the method, the reference to the class instance that the method belongs to, as well as the parameters sent to the method
* When a thread terminates, its stack frame is destroyed by the JVM

## Native Method Stack (per Thread)

* Java threads are directly mapped to native operating system threads
* After a Java thread is prepared, a native thread is created to store native method information invoked through the Java Native Interface
* Once this native thread has been created, it calls the Java thread's `run` method

## Just-In-Time Compiler

* If a method is called multiple times, interpreting it each time can be optimized by simply storing the interpreted instructions in a cache
* The JIT compiles the bytecode to native code (machine code) and then provides the machine code when a a repeated method is called
* The JIT does take longer to compile than a single call to the interpreter so it doesn't make sense to call the JIT for a piece of logic that is only executed once
* The JIT will check the frequency of various method calls and decide which ones to compile (adaptive compiling)
