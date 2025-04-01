# [Designing Software for Ease of Extension and Contraction](https://courses.cs.washington.edu/courses/cse503/08wi/parnas-1979.pdf)

## Software as a Family of Programs
* Some of the ways that members of a “program family” may differ are
* They may run on different hardware configurations
* They may perform the same functions, but differ in the format of the input / output data
* Differ in certain data structures / algorithms due to available resources
* Some users only need a subset of the services / features of other users

## How Does the Lack of Subsets and Extensions Mainfest Itself?
* Programs are structured as a chain of components, each receiving data from the previous component, processing it, changing the format, before sending the data to the next program in the chain
* When an unnecessary component is removed from this chain this can cause complications because the output of its predecessor is incompatible with the successor component

### Components that perform more than one function
* Another common error is to combine two simple functions into one component because the functions seem too simple to separate
* Example: combine synchronization with message sending and acknowledgement
* For the sake of reliability, insist on message acknowledgement with each exchange of synchronization signals
* If an application needs rapid synchronization signalling, there might not be a simple way to strip out message sending

### Loops in the “Uses” Relation
* Because of the reuse of existing programs, one may end up with a system in which nothing works until everything works
* Example: operating system scheduler that uses the file system to store data, and thus the file system must be present and working before any task scheduling is possible
* There are users for whom an operating system subset without a file system would be useful
* Even if these users don’t exist, the subset may be useful in testing and development

### Information Hiding: Interface and Module Definition
* If a programmer must develope a family of products, the programmer tries to isolate the changeable parts in modules
  * The programmer develops an interface between the module and the rest of the product
* Design intermodule interfaces that are insensitive to the anticipated changes
  * The changeable aspects or "secrets" of the modules are not revealed by the interface
* Another term for "information hiding" is encapsulation or abstraction
* If one program uses another directly, the presence of the second program cannot be fully hidden from its user
* However, there is never any reason for a component to "know" how many other programs use it

### The Virtual Machine (VM) Concept
* Stop thinking of systems in terms of components that correspond to steps in processing
* The viewpoint that seems most appropriate for designing software families is often termed the virtual machine approach
* Hardware machine provides a set of instructions that operate on a small set of data types
* Virtual machine extends those operations to operate on additional data types
  * These operations are "software instructions"
* The programmer writing programs for the virtual machine does not need to distinguish between instructions that are implemented in software and those that are hardware instructions
