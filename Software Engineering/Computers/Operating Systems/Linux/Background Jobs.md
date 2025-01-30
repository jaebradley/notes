# Background Jobs

* List jobs using `job` command
* Bring target job to the foreground with `fg` like `fg %1`
* `CTRL` + `Z` to suspend
* To start running the job to the background use `bg` like `bg %4`

## [Redirection](https://unix.stackexchange.com/questions/119648/redirecting-to-dev-null)
* Redirect `stdout` and `stderr` to `/dev/null` (or a log file) to prevent background processes from sending messages to the terminal
  * `some command 1> /dev/null 2> /dev/null &`
* File descriptor `1` is the default file descriptor for `stdout`
  * `1>` can be replaced with just `>`
* File descriptor `2` is the default file descriptor for `stderr`
* File descriptor `0` is the default file descriptor for `stdin`

## [Using `nohup`](https://unix.stackexchange.com/a/103777)
* Downside is that it generates a log file by default in the current directory, but if you close out of the current shell, the process is not killed
* Can update the `nohup`  command to do output redirection
  * `nohup command 1> some.log 2> some.log`
* In Bash 4, redirecting `stdin` and `stderr` can be abbreviated as `&>`