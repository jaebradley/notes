# [I/O Redirection](https://tldp.org/LDP/abs/html/io-redirection.html)

* There are three default files open - `stdin`, `stdout`, and `stderr`
* Each open file gets assigned a file descriptor
  * `stdin` descriptor is `0`, `stdout` is `1`, `stderr` is `2`
* `>` redirects `stdout` to a file - creates the file if not present, otherwise overwrites it
* `>>` Redirect `stdout` to a file
  * Creates the file if not present, otherwise appends to it
* `1>some_file` - redirect `stdout` to `some_file`
* `2>some_file` - redirect `stderr` to `some_file`
* `&>some_file` - redirect both `stdout` and `stderr` to `some_file`
* `bad_command >>filename 2>&1`
  * Appends both `stdout` and `stderr` to the file
