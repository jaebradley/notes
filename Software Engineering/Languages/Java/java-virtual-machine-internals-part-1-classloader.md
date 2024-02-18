# [Java Virtual Machine (JVM) Internals, Part 1 — Classloader](https://medium.com/javarevisited/java-virtual-machine-internals-class-loader-eea706eb37d9)

* A Java virtual machine's main job is to load class files and execute the bytecodes they contain
* The Classloader loads class files from both the program and the Java API
  * Only the class files from the Java API that are actually needed by a running program are loaded into the virtual machine
* Class loading is finding and loading types (classes and interfaces) at runtime dynamically
  * Types data are contained in binary files in class file format
  * It also verifies the correctness of imported classes, allocates and initializes memory for class variables and assists in the resolution of symbolic references
* Order of activities
  * Loading - finding and importing the binary data for a type with a particular name and creating a class or interface from that binary representation
  * Linking - performaing verification, preparation, and (optionally) resolution
  * Verification - ensuring the correctness of the imported type
  * Preparataion - allocating memory for class variables and initializing the memory to default values
  * Resolution - transforming symbolic references from the type into direct references
  * Initialization - invoking Java code that initializes class variables to their proper starting values

## Class Loading Mechanism

* Every class loader has a "parent" class loader
* When loading a class, the class loader first delegates searching for the class to the parent class loader before attempting to find the class itself
* This builds a graph of class loaders where the bootstrap class loader is at the root of the graph
* First, there is a cache lookup where the class loader asks if it has loaded the same class before
  * If not, it asks parent the same question (and this repeats recursively)
  * If nothing is found in the caches, the class loader searches its own path for the source of the class
* Because the parent class loader is always given the opportunity to load a class first, the class is loaded by the class loader nearest the root
  * This also implies that class loaders only see classes loaded by itself or its parent or ancestors - it cannot see classes loaded by its children

## Runtime Built-in Class Loaders

* The application class loader first searches the named modules defined to all of the built-in loaders
* If a suitable module is defined to one of these loaders then that loader will load the class
* If a class is not found in a named module defined to one of these loaders, then the application class loader delegates to the parent
* If a class is not found by the parent, the application class loader searches the classpath
* Classes found on the class path are loaded as members of this loader's unnamed module
