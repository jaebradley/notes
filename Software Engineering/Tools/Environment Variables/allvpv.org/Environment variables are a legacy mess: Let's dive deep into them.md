# [Environment variables are a legacy mess: Let's dive deep into them](https://allvpv.org/haotic-journey-through-envvars/)
* Environment variables are passed from parent to child
* On Linux, a program uses the `execve` syscall to excute another program, preceded by `clone`/`fork`
* While all envvars are passed from parent to child, nothing prevents a parent process from passing a complete different set of environment variables when calling `execve`
  * For example, the `login` executable sets up a fresh environment for its children
* `ls -lah`
  * `/usr/bin/ls` is the `filename` (i.e. the executable path)
  * `['ls', '-lah']` is the `argv` array of command line arguments
  * `['PATH=/bin:/user/bin', 'USER=allvpv']` is the `envp` array of envvars
* After launching the program, kernel dumps the variables on the stack as a sequence of null-terminated strings

## Bash
* Bash stores the variables in a stack of hashmaps
* Newly spawned Bash processes traverse the stack of hashmaps to find exported variables and copy them to the environment array that is then passed to the child process
* The stack needs to be traversed because each function invocation in Bash creates a new local scope, which is a new entry on this stack
  * A `local`-declared variable ends up on the locally-scoped hashmap
  * These `local` variables can also be exported, and are _not_ in the global scope

## Liberal Format
* Kernel accepts any null-terminated string as an environment variable definition - does not even need to be in the form `KEY=VALUE`
* Definition size limit of `page size x 32`
  * Most modern hardware uses 4 KB page sizes, so this is effectively a 128 KB limit
* Total environment variable definition of ~2 MB
  * Definition is `max(32 x page size, min(max stack size / 4, 6 MB))`
  * On typical systems, the limiting factor is the max stack size as envvars are initially dumped on the stack
  * Systems only allow 25% of the stack to be dedicated to environment variables

## The standard format
* A popular misconception is that POSIX only permits uppercase envvars, but that doesn't match the standard
  * "The name space of environment names containing lowercase letters is reserved for applications"
* Only strict rule is that a variable name cannot contain an equals sign
* Use the POSIX-mandated Portable Character Set to be safest (ASCII without control characters)
