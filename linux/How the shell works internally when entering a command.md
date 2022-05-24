# [How the shell works internally when entering a command](https://medium.com/@muxanz/how-the-shell-works-internally-when-entering-a-command-42f08458870)

* Shell saves the command in memory - tokenizing the command into the executable (like `ls`), the arguments (like `-la`)
* The shell will try and locate an executable matching the name of the command via the `PATH` environment variable
* The shell executes the command by making a system call to create a new process that is a child of the shell process (the parent process)
* The parent process makes a `execve` system call, which executes the command (`ls -la`) within the child process
* The child process will then execute the binary, writing the content to standard out, and then exiting
* When `execve` has finished, the child process will be terminated with a system call `wait`, which waits for the termination of the child process before returning to the parent process
