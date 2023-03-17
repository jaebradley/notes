# [10 Vim Tricks You Should Know](https://medium.com/hacking-and-gonzo/10-vim-tricks-you-should-know-6393842b3537)

* `*` searches forward for word under cursor
* `#` searches backward for word under cursor
* When in insert mode, `ctrl + N` will try and complete current word with first match in current file
  * `ctrl + P` searches backwards
* `.` repeats previous change
* `%` jumps to matching opening or closing brace
* Indent current line using `==`
* When in visual mode (`shift + V`) `=` will indent the selected lines
* `u` to undo a change
* `ctrl + R` to redo a change
* `U` to return line to original state
* `g-` or `g+` to go to text change before or after
* `:earler 1m` to go to change a minute ago
* `:later 1m` to go to change one minute in the future
* `:set incsearch` to set search option that will highlight matches while typing
* `:set hlsearch` to highlight all search pattern matches
* To disable highlighting search `:nohlsearch`
* Record actions
  * `qa` to start recording actions to `a` register
  * To stop recording press `q` again
  * To replay actions, `@a`
