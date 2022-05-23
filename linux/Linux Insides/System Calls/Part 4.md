# [System calls in the Linux kernel. Part 4.](https://0xax.gitbooks.io/linux-insides/content/SysCall/linux-syscall-4.html)

* The `bash` shell like any C program starts from the `main` function, which tries to do things like open `/dev/tty`, loads `.bashrc` etc
* Eventually the `shell_execve` function calls the `execve` system call, which takes a binary (like `/bin/ls`), arguments (`["ls"]`), and any relevant environment variables (represented as an array)

## execve system call

