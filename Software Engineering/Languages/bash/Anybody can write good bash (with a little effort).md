# [Anybody can write good bash (with a little effort)](https://blog.yossarian.net/2020/01/23/Anybody-can-write-good-bash-with-a-little-effort)

* Has a shebang and that shebang is `#!/usr/bin/env bash`
  * not all systems have a (good) version of GNU bash at `/bin/bash`: macOS infamously supplies an ancient version at that path, and other platforms may use other paths
* Bash scripts should have a top level comment that briefly explains its functionality
* Must be able to run from any directory or fail loudly if it isn't run from the correct directory
* To check for an installed binary, could do something like [`type -p "${name_of_binary}"`](https://www.reddit.com/r/programming/comments/esu8gu/comment/fg20i3a/?utm_source=share&utm_medium=web2x&context=3)
* At top of script, check if bash version is the one that is required
* Instead of storing the output of a command to a file, which will then be read by another commamd, just pipe the output using process substition to the next command
  * `second-stage <(first-stage)`
