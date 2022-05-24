# [System calls in the Linux kernel. Part 4.](https://0xax.gitbooks.io/linux-insides/content/SysCall/linux-syscall-4.html)

* The `bash` shell like any C program starts from the `main` function, which tries to do things like open `/dev/tty`, loads `.bashrc` etc
* Eventually the `shell_execve` function calls the `execve` system call, which takes a binary (like `/bin/ls`), arguments (`["ls"]`), and any relevant environment variables (represented as an array)

## execve system call

* Eventually calls down to `do_execveat_common`, which takes 5 arguments
  * A file descriptor that represents the directory of the application, which is the current working directory of the calling process
  * takes the three arguments passed to the system call
  * takes a set of flags - in this case `0`
* `do_execveat_common` checks that the number of running processes for the current user is not exceeded
* Allocate memory for a struct that holds the arguments to load a binary
* Check the file descriptor of the executable binary
  * Check the name of the binary file starts from the `/` symbol, or if the path of the executable file is relative to the current working directory of the calling process
* Read the first `128` bytes from the binary executable file to check the type of executable
* Created a struct with the program's stack, and set a pointer to top of the stack - top of the stack contains the program filename
* `search_binary_handler` function goes through the list of handlers that contain different binary formats like ones that start from the `#!` line, the `elf` format, etc
* If a binary handler is found, like the `elf` binary handler, the binary handler does many things to prepare an executable file for execution like check the architecture and the type of the executable file
  * The binary handler also reads the program interpreter and related libraries from disk and load it to memory
  * Sets up the stack and maps the binary into the correct location in memory
* At the end of the binary handler, it calls a function with the set of registers for the new task, the address of the entry point of the new task, and the address of the top of the stack for the new task
  * The function sets the instruction pointer, and effectively prepares a thread to run in userspace
  * Return up the call stack until return to `do_execveat_common`, at which point, we should have released memory for structures that were previously allocated
* After return from the execve system call handler, execution of the program will start
