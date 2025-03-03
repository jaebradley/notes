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
