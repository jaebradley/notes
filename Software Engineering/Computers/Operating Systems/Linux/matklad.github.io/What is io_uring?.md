# [What is io_uring?](https://matklad.github.io/2024/09/23/what-is-io-uring.html)
* `io_uring` is a Linux kernel interface for making system calls
* System calls are usually submitted to the kernel individually and synchronously
  * System call CPU instruction transfers control from the application to the kernel
  * Control returns to the application only when the system call is completed
* `io_uring` is a batched and asynchronous interface
* Application submits several system calls by writing their codes and arguments to a lock-free shared-memory ring buffer
* Kernel reads the system calls from shared memory and executes those system calls
* To communicate the system call results back to the application, the kernel writes the results back to a second lock-free shared-memory ring buffer
