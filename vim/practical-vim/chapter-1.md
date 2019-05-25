# Chapter 1

* `.` command is very powerful (repeats last change)
  * Last change is basically all keystrokes until leave insert mode
* Example of appending semi-colon at end of each line
  * `$a;`
  * And then can do `j$.` to take advantage of dot command
  * But even simpler is `A;` as it appends at the end of current line (basically squashes `$a` into single command)
* `s` deletes character under cursor and enters insert mode
* `f{char}` tells vim to look ahead for next occurrence of specified character in the line and then cursor to it
  * `;` will repeat the last search for `f`
* Same thing for `t{char}`
* `F` and `T` look backwards
* `/{pattern}` to scan for next match
  * `?` for previous
* `*` searches for word under the cursor
