# [What happens when you open a terminal and enter ‘ls’](https://www.warp.dev/blog/what-happens-when-you-open-a-terminal-and-enter-ls?utm_source=pocket_reader)

## History - From Teletypes to Terminal Emulators

* ASCII text transmitted over a physical wire from user to computer
* Kernel receives the characters and decode them
* Characters are sent to TTY driver
  * This kernel module is responsible for sending the user input to user programs and collecting the output
* Kernel sends output back to the teletype for display to the user
* Line discipline, buffered characters into kernel memory
  * Program wouldn't receive input until "Enter" was pressed
  * Performance optimization since asking the program to react to every individual character was inefficient

## Opening the Terminal App

* To service the full spectrum of use-cases (like writing scripts, conditional logic, running loops, etc) we want a full, interactive, interpreted programming environment
* A shell does the work of running other programs as processes and interpreting commands you write
  * Bash, Zsh, and fish are examples of popular shells
  * The terminal and shell are separate programs with separate concerns
    * The shell is concerned with the contents of the typed commands
    * The terminal is concerned with UI-related things like fonts, colors, tabs, scrolling
* Terminal needs to spawn a process for the shell the user wants, as well as a method for communicating with the shell and any processes the shell starts

## Creating a “PTY”

* Terminal emulators work by streaming characters that users type to file descriptors called PTY (pseudo-TTY)
  * There are actually pairs of file descriptors that represent the two ends of the physical wire that transmitted user-entered input to programs, which also sent back output
* Terminal asks the kernel to creat these files
* Kernel still has a TTY driver with a line discipline for mediating data between the two ends of the PTY
  * One end of the PTY is the "leader" and the other end is the "follower" (which is used by the shell and all other processes created in the session)
* The PTY file descriptors are virtual character devices
  * "leader" PTY file descriptor points to a buffer in memory
  * "follower" PTY file descriptor points to a character device file with an actual path on disk

## Spawning the Shell

* Before receiving the user's first command, the terminal has to spawn the shell process
 * The shell process is the first child process of the terminal session
* Shells are fully interactive programming language interpreters
* Shell creates the child processes for each command that the user wants to run
* Spawned shell process reads and writes from the "follower" PTY
 * Shell's stdin/stderr/stdout (fd 0-2) are set to the "follower" PTY
* Input path: user input -> terminal -> "leader" PTY -> TTY driver (Kernel) -> "follower" PTY -> shell
* Output path: shell -> "follower" PTY -> TTY driver (Kernel) -> "leader" PTY -> terminal -> write to display

## Entering keystrokes

* Keystrokes are translated into ASCII characters
* ASCII is written to the "leader" PTY
* TTY driver reads characters from the "leader" PTY, and stores the characters in its line discipline
* Line discipline acts as an intermediate buffer between the PTY ends
 * Line discipline job is to interpret the characters from the "leader" PTY and then process them
 * When the line discipline receives the backspace character, it interprets this by removing the last character in its internal buffer, and then writing the deletion back to the "leader" PTY
  * "follower" PTY is not written to
 * When the line discipline receives CTRL-C, the line discipline interprets it as an INTERRUPT character and sends a SIGINT to the "follower" PTY
  * This SIGINT will interrupt any processes running in the foreground (background processes are unaffected)
  * Line discipline will also write the characters back to the "leader" PTY
  
## ‍Parsing a command

* Command is tokenized / syntactically and semantically analyzed
* Each shell comes with its own programming language so syntax and semantic analysis is done differently from shell to shell
* Shell searches through various token references
 * aliases - mapping between user-defined words and other tokens
 * functions
 * environment variables
 * builtins - predefined set of commands implemented within the shell executable itself
 * `PATH` executables - external commands that the shell can locate via the `$PATH` variable (`git` etc)
 * `type` command outputs how the shell will resolve the token
* Executables are separate programs that are located on the filesystem and are executed as a separate process
* When the shell locates the executable, it will fork a process and run the executable in the child process
* For example, echo is usually both a shell builtin but also available as an executable. Shells will offer builtin alternatives to executables when they can be more efficiently run within the shell process itself rather than forking and running another process. Some commands, like cd, just can’t be executables because they need to mutate the shell process’s state. If cd were to be run as an executable, it would run in a child process and modify the child process’s working directory as opposed to modifying the shell process’s working directory

## Escape Sequences

* Programs, like the shell, can emit escape sequences to have control over the terminal, like adding text decoration
* `ls --color` will print directories a different color because there is an escape sequence to first change the foreground color before the characters for the directory names are emitted
* It's the terminal's job to actually render the characters on the screen with an appropriate color
* Example sequence: `ESC[1mESC[36mdir/ESC[39;49mESC[0m`
 * `ESC[1m` = turn on bolding
 * `ESC[36m` = set forground color to cyan
 * `dir` = text
 * `ESC[39;49m` = set the default fg color and default bg color
 * `ESC[0m` = reset all
 
‍
