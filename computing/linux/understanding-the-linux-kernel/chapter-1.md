# Chapter 1: Introduction

* Each computer system includes a basic set of programs called the operating system
* The most important program in the set is called the kernel - it is loaded into RAM when the system boots
  * Often times the term "operating system" is used as a synonym for "kernel"
* A Unix-like operating system hides all low-level details concerning the physical organization of the computer from applications run by the user

## Users and Groups

* In a multiuser system, each user has a private space on the machine where they own some quota of the disk space to store files, receive private mail messages, etc
* There is a unique number called the User ID / UID to identify a person
* Every file is associated with exactly one user group
* Any Unix-like operating system has a root / superuser who can do almost anything, because the operating system does not apply the usual protection mechanisms
  * The root user can access every file on the system and can manipulate every running user program

## Processes

* A process is either an instance of a program in execution, or the execution context of a running program
* A process executes a single sequence of instructions in an address space
  * Address space is the set of memory addresses that the process is allowed to reference
* The scheduler chooses the process that can progress
  * Some operating systems allow only nonpreemptable processes, which means that the scheduler is only invoked when a process voluntarily relinquishes the CPU
  * A multi-user system (like Unix) must have preemptable processes - the operating system must track how long each process holds the CPU and periodically activate the scheduler
* Unix-like operating systems adopt a process/kernel model
  * Each process has the illusion that it's the only process on the machine 
  * When a process makes a system call, the hardware changes the privilege mode from User Mode to Kernel Mode, and the process starts the execution of a kernel procedure with a strictly limited purpose
  * After the request is satisfied, the kernel procedure forces the hardware to return to User Mode and the process continues its execution from the instruction following the system call

## An Overview of the Unix Filesystem

### Files

* A Unix file is an information container structured as a sequence of bytes - the kernel does not interpret the contents of a file
* Most filesystems place a limit on the length of a filename (typically no more than 255 characters)
* Unix associates a current working directory with each process - it belongs to the process execution context

### Hard and Soft Links

* The Unix command `ln first_path second_path` creates a new hard link (a filename in a directory) for the path defined by `second_path` for the file at `first_path`
* Cannot hard link directories
* Links can only be created among files on the same filesystem - on many modern Unix systems, users may be unaware of the physical divisions between filesystems (they might be on different disks or partitions)
* Soft links / symbolic links (`ln -s first_path second_path`) means that the filesystem creates a new netry in the directory that `second_path` refers to
  * This entry is of type "symbolic link" and has the name of the file in `second_path`
  * Each reference to `second_path` can be translated automatically as a reference to `first_path`

### File-Handling System Calls

* When a user accesses the contents of either a regular file or a directory, they actually access some data stored in a hardware block device
  * In a way, a filesystem is a user-level view of the physical organization of a hard-disk partition
  * Because a process in User Mode cannot directly interact with the low-level hardware components, each actual file operation must be performed in Kernel Mode
* So `open(path, flag, mode)` makes a system call that creates an "open file" object 
  * This system call returns an identifier called a file descriptor to the process
  * A file descriptor represents an interaction between a process and an opened file - the open file object contains data related to that interaction
  * The same open file object may be identified by several file descriptors in the same process
  * When the same file is opened between multiple processes, each process is assigned a separate open file object

## An Overview of Unix Kernels

### The Process/Kernel Model

* When a program is executed in User Mode, it cannot directly access the kernel data structures or kernal programs (these restrictions do not apply in Kernel Mode)
* Each CPU provides special instructions to switch between User Mode and Kernel Mode
* The kernel itself is not a process but a process manager
* Processes that require a kernel service use a specific programming construct called a system call
  * Each system call sets up the group of parameters that identifies the process request and then executes the hardware-dependent CPU instruction to switch from User Mode to Kernel Mode
* Kernel routines can be activated in several ways other than a system call
  * The CPU executes a process and signals an exception, like an invalid instruction - the kernel handles th exception on behalf of the process that caused it
  * A peripheral device issues an interrupt signal to the CPU to notify it of an event like a status change or completion of an I/O operation
    * The kernal has a program called an interrupt handler that deals with each interrupt signal
    * Because peripheral devices operate asynchronously with respect to the CPU, interrupts occur at unpredictable times

### Process Implementation

* Each process is represented by a process descriptor
  * Contains several process registers like the stack pointer registers, memory management registers to keep track of the RAM accessed by the process (etc.)
  * When the kernel resumes executing the process, it uses the process descriptor fields to load the CPU registers and the process resumes execution at the point where it was stopped
* When a process is not executing on the CPU, it is waiting for some event
  * Unix kernels have queues of process descriptors where each queue represents a set of processes waiting for a specific event

### Reentrant Kernels

* All Unix kernels allow several processes executing in Kernel Mode at the same time (i.e. "reentrant")
* If a hardware interrupt occurs, a reentrant kernel is able to suspend the current running process, even if that process is in Kernel Mode
  * This improves the throughput of the device controllers that issue interrupts
  * If the kernel is able to answer the interrupt quickly, the device controller will be able to perform other tasks while the CPU handles the interrupt
* A _kernel control path_ denotes the sequence of intructions executed by the kernel to handle a system call, an exception, or an interrupt

### Process Address Space

* Each process runs in its private address space
  * When in User Mode, the process has it's own private stack, etc
  * In Kernel Mode, the process has access to the kernel data (and also has another private stack)

### Synchronization And Critical Regions

* If a kernel control path is suspended while acting on a kernel data structure, no other kernel control path should be allowed to act on the same data structure unless it has been reset to a consistent state

#### Semaphores

* A semaphore is simply a counter associated with a data structure
  * An interger counter variable
  * A list of waiting processes
  * Two atomic methods (`down` and `up`)
* If `down` decreases the value of the semaphore to less than `0`, then adds the currently running process to the semaphore's list and then is blocked (i.e. the scheduler is invoked)
* If `up` increase the value to `>=` 0, then one or more of the processes are reactivated on the semaphore's list
* Each data stucture that needs to be protected has its own semaphore (initialized with a count of `1`)
* When a kernel control path wishes to access the data structure, it executes `down`
  * If the value of the new semaphore isn't negative, access to the data structure is granted
  * Else, the process that is executing the kernel control path is added to the semaphore list and blocked
* When another process executes the `up` method on the semaphore, one of the processes in the semaphore list is allowed to proceed

#### Spin Locks

* If the time to update a protected data structure is short, a semaphore could be inefficient since checking a semaphore involves inserting a process in the semaphore list and then suspending it
* When a process finds the spin lock is closed by another process, it "spins" around repeatedly, executing a tight instruction loop until the lock becomes open

### Process Management

* Unix distinguishes between the process and the program it eecutes
* The `fork` and `_exit` system calls are used to create and terminate processes
* The `exec` system call is executed to invoke loading a new program
  * The process resumes execution with a brand new address space containing the loaded program
* The process that invokes `fork` is the parent process while the `fork`ed process is the child process
  * Each process has a pointer to it's parent and children

#### Zombie Processes

* The zombie process state occurs when a process is terminated until the parent process executes a `wait4` system call on it
* If a parent process terminates without issuing a `wait4` call to it's children processes, the `init` system process (created during system initialization) will periodically monitor these "orphaned" zombie processes to remove them
  * When a process terminates, the kernel changes the appropriate process descriptor pointers of all the existing children of the terminated process to make them become children of `init`
  * The `init` process monitors execution of all its children and routinely issues `wait4` calls

### Memory Management

#### Virtual Memory

* Virtual memory acts as a logical layer between the application memory requests and the hardware memory management unit
* The virtual address space is a set of memory references that a process can use is different from the actual physical memory addresses
* When a process uses a virtual memory address, the kernel and memory management unit cooperate to find the actual physical memory address of the requested value in memory

#### RAM

* A few MB are dedicated to storing the kernel image (kernel code and kernel static data structures)
* Remaining RAM is handled by the virtual memory system and is used
  * to satisfy kernel requests for buffers, descriptors, and other kernel data structures
  * to satisfy process requests for generic memory areas and for memory mapping files
  * enable better performance from disks and other buffered devices by caching values
