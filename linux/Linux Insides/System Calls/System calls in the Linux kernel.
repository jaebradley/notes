# [System calls in the Linux kernel. Part 1.](https://0xax.gitbooks.io/linux-insides/content/SysCall/linux-syscall-1.html)

## System call. What is it?

* A system call is a userspace request of a kernel service
  * The operating system kernel provides many services like writing or reading from a file, listening for connections on a socket, deleting or creating directories
  * A system call is a C kernel space function that user space programs call to handle some request
* Assembly language "Hello World" example where the `data` section stores the initialized data for program and the `text` contains the program instructions
* There's a set of program instructions that make `syscall` instructions
* The `syscall` instruction jumps to the address stored in the `MSR_LSTAR` (Model-Specific Register, Long System Target Address Register)
  * Kernel is responsible for providing its own custom function for handling syscalls and writes the address of the handler function to the `MSR_LSTAR` register
* Each system call has it's own unique number - for example, the `write` system call has the number `1`
* A number of registers are used to make the system call - the `rax` register is used to hold the number of the system call (in the first system call, this is `1` for `write`), the `rdi`, `rsi`, and `rdx` registers take the file descriptor (`1` for standard out), pointer to the string `Hello World`, and the length of the string, as arguments to the `write` system call
* The second system call in the example is the `exit` system call, and takes a single parameter, the return value
* In general, arguments of a function are placed in registers or pushed on the stack, and at least for the x86-64 examples that are shown, there is an explicit ordering to the registers (`rdi`, `rsi`, `rdx` etc)
  * If there are more than the 6 defined registers, the remaining arguments are pushed on the stack
* The `fopen`, `fget`, `printf`, etc functions in the `C` standard library are functions that are just wrappers for the system calls
* The `ltrace` util displays the set of userspace calls of a program
  * Using the `-S` option can display the underlying system calls, like seeing that `puts` calls the `write` system call

## Implementation of the write system call

* The `SYSCALL_DEFINE3` macro takes the name of the system call and some parameters
  * This macro will enable tracing to catch entry and exit events to the system call, if a kernel configuration option is enabled
* There's a function called `fdget_pos` that gets the file descriptor table of the current process, and tries to find a corresponding file descriptor number there of the file descriptor passed to the `write` system call
  * Then get the current position in the file with the `file_pos_read` function that returns the `f_pos` field in the file
  * The `vfs_write` function writes the given buffer to the given file starting from the given position
  * If the `vfs_write` function finishes successfully, update the position in the file via the `file_pos_write` function (which just updates the `f_pos` field in the file)
  * Finally, there's a call to the `fdput_pos` function that unlocks the `f_pos_lock` mutex that protects file position during concurrent writes from threads that share the file descriptor
