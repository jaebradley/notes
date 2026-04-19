# [How Does Java Really Work? Inside the JVM Architecture](https://codiescoder.substack.com/p/java-architecture)
* Think of bytecode as a universal language
  * The JVM on the physical machine will translate it into machine code that is specific to the computer's processor
* `.class` files are outputted when a Java program is compiled
  * The `.class` files contain bytecode

## Class Loaders - Bringing Classes into Memory
* When a class is first referenced by a program, the JVM's class loader loads the class file into memory
* The bootstrap class loader loads core Java libraries
  * Has the highest priority and is built into the JVM
* The extension class loader loads Java extension libraries (like `javax.*`)
  * Second-highest priority
* Loads classes from Java application, including external libraries specified in the classpath like `.jar` files

## Linking
* Verification step that ensures the bytecode is valid
* Preparation step allocates memory for static variables, initializes them to default values
* Resolution step resolves symbolic references, like class or method names into actual references to memory locations

## Initialization
* Static variable value assignment
* Static block execution
  * Static blocks run only once, the first time a class is accessed
* Instance variables are assigned default values during class loading
  * Instance variables now are assigned their actual values, either from the constructor or static block execution
* Classes are initialized when
  * The `new` keyword is used
  * A static method is called
  * A static field is assigned a value
  * Explicit class initialization
  * Reflection
  * An instance of a subclass

## Runtime Data Area - The Brain of the JVM

### Method Area
* The method area is a shared resource across all threads in the JVM
* It holds the following class information
  * Metadata like class name, superclass
  * Method and field data like method names, parameter types, return types, etc
  * Runtime constants like literal constants, references to other classes/methods
  * Constructor and method bytecode for both instance and static methods
* If the JVM can't allocate memory due to excessive class loading, it will throw an OOM error

### Heap Area
* Shared across all threads
* The runtime data area where all class instances and arrays are allocated
* JVM uses automatic garbage collection to manage heap memory
* While the heap can be dynamically resized during the runtime, if the JVM can't allocate sufficient memory, it throws an OOM error

### Stack Area
* Each JVM thread gets a stack area
* Stack is used to manage local variables
* Each method invocation creates a stack frame
* Stack frames store local variables, operand stack for intermediate computate, frame data (like method data, exception information)
* If a stack grows too large, the JVM will throw a `StackOverflowError`
* If the JVM cannot allocate enough memory for a new stack, it throws an OOM error

### Program Counter (PC) Register: The Instruction Pointer
* Each JVM thread has its own Program Counter Register
* This register holds the address of the currently executing instruction

## Execution Engine - Running Bytecode
* JVM reads and executes bytecode line-by-line
* JIT (Just-In-Time) compilation: the JVM tracks which methods are called frequently
  * These hotspots are compiled into machine code and can be referenced without needing to be compiled into machine code
* Java Native Interface allows Java to interact with native code (like C/C++ libraries)
  * These operations are operating system specific
  * Java loses its cross-platform advantage when using native code
