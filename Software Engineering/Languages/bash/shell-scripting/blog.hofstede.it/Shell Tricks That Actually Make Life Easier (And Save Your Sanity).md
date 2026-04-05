# [Shell Tricks That Actually Make Life Easier (And Save Your Sanity)](https://blog.hofstede.it/shell-tricks-that-actually-make-life-easier-and-save-your-sanity/)
* `> file.txt` empties a file without deleting and recreating it
  * Preserves file permissions, ownership, doesn't interrupt processes that already have the file open
  * Cleaner than `echo "" > file.txt` or `echo -n "" > file.txt`  or `rm file.txt && touch file.txt` 
* `$_`: in most shells, `$_` expands to the last argument of the previous command
  * This is especially useful interactively or in scripts that operate on the same long path twice
  * Example: `mkdir -p /some/ridiculously/long/path/newdir && cd "$_"`
* `fc` is the more portable sibling to `Ctrl` + `x`, `Ctrl` + `e` and opens the previous command in the `$EDITOR` 
* `ESC` + `.` or `ALT` + `.` inserts the last argument of the previous command at the cursor
  * Repeatedly press it to cycle further back in history
  * `!$` is the non-interactive sibling
    * Expands when a command is executed (can use `$_` variable)
* Copy/rename using brace expansion
  * `cp file.conf{,.bak}` expands to `cp file.conf file.bak` when oen wants to edit a critical config file and wants to make a backup first
  * `mv file.{txt,md}` expands to `mv file.txt file.md` 
  * `mkdir -p foo/{src,tests,docs}` creates all three directories at once
* `<(command)` treats the output of a command like a file
  * Example: diffing the sorted versions of two files
  * `diff <(sort file1.txt) <(sort file2.txt)` 
* `Ctrl` + `Z`, then `bg`, then `disown`
  * Execute a long-running background process but not in `tmux`
  * `Ctrl` + `Z` suspends / pauses the process
  * `bg` resumes process executing in the background
  * `disown` detaches from the shell
* `|&` pipes to both standard out and standard error
  * Standard pipes only catch standard output
  * A script that prints to standard error will not pipe the standard error output, but instead print the standard error to the executing shell
  * `|&` is a shorthand for `2>&1 |`
