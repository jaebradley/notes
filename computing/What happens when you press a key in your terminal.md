# [What happens when you press a key in your terminal?](https://jvns.ca/blog/2022/07/20/pseudoterminals/)

## example: `ls`

* `ls` results in the client sending `l` and then receiving an `l` "echoed" back by the server
* Same thing for `s`
* When a client sends a newline (i.e. `enter`), the server responds with some escape sequences for displaying the directory contents and various lines of content

## example: `Ctrl+C`

* `Ctrl+C` results in the client sending `\x03`, which is "End of Text" in an ASCII table
* When the server receives "End of Text" it recognizes that it means "interrupt", and then sends a `SIGINT` to the process that owns the terminal's process group
  * Interruption is handled in the kernel and not in userspace
* `Ctrl+D` is `\x04`, which corresponds to "End of Transmission" in an ASCII table

## `Ctrl+letter`

* The byte that gets sent is the 1-indexed value of the letter in the alphabet (i.e. `a` is `1`, `b` is `2`, `z` is `26`)

## ANSI escape sequences

* Escape sequences start with `\x1b` (the escape character)
* Escape sequences can change the cursor's position, text formatting / colors
* Escape codes are why your terminal can change formatting if binary is `cat`ted to the terminal - eventually random `0x1b` bytes will affect the terminal formatting
* A lot of programs implement a timeout for escape codes - if another key is not pressed after some minimum amount of time, it's not interpreted as an escape code anymore
  * Author uses `fish`, and there's a `fish_escape_delay_ms` configuration value
