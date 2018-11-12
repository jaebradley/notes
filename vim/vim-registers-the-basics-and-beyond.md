# [Vim Registers - The Basics and Beyond](https://www.brianstorti.com/vim-registers/)

* Every register is accessed using a double quote - content that is in register `r` with `"r`
* `"ry"` - yanks the selected text and adds it to register `r`
* `"rp"` - pastes the data in the register
* Registers can also be accessed in insert mode via `Ctrl + r` (or whatever the register name is)
* `:reg` to see registers
  * `:reg a b c` only shows registers `a`, `b`, `c`
* Unnamed register `""`
  * Any text that is deleted or yanked is placed there
* Automatically stores numbered registers from `0` to `9` where `0` is latest yank
* `"."` - last inserted text
* `"%"` - current file path
* `":"` - the most recently executed command
* `"=` - expression register
  * In insert mode, `Ctrl + r + =`, then type `2 + 2`, `4` will then get get stored to the expression register
* Search register, latest text that you searched with
  * If you searched for `/Nietzsche`, no way going to type `Nietzsche` again, use `:%s/<Ctrl-r />/<some string>/g`
