# Chapter 10: Copy and Paste

* Transposing characters example
  * `Practical lvm`
  * `F<space>` to get to space
  * `x` to remove space and cursor is on `l`
  *  `p` pastes space after cursor position
  * Paste occurs due to unnamed register
  * `xp` can be considered "transpose the next two characters"
* Transposing lines
  * `dd` deletes line and places it in unnamed register
  * `p` knows its dealing with line so it pastes contents of unnamed register after current line
  * `ddp` can be considered "transpose the order of this line and its successor"
* Using other registers
  * If a register is not specified, vim uses unnamed register
  * `"ayiw` - yank current word into register `a`
  * `bdd` - yank current line into register `b`
  * `"ap` - paste word from register `a`
* Explicit way of calling unnamed register is `""`
* `x`, `s`, `d`, `c`, and `y` commands all set contents of the unnamed register
* When the `y` motion is used, the text is copied to the unnamed register and the yank register, which is addressed by `0` symbol
  * So can paste from yank register like `"0P`
* Use "black hole register" (`_`) to delete text without overwriting unnamed register (for example)
  * `"_d{motion}`, for example
* Plus register (`+`) references system clipboard
  * Paste from clipboard using `"+p"`
  * Can also use `Ctrl+r`, `+` in insert mode
  * Can also capture yank / delete to system clipboard with `"+`
* Read-only registers
  * `"%` - Name of current file
  * `".` - Last inserted text
  * `":` - Last executed command
  * `"/` - Last search pattern
* Can also use Visual selection to replace contents of a given register
  * Side-effect is that when using `p` in Visual mode, it uses value from unnamed register and then sets value of unnamed register to text it's replacing
  * This means that undoing is a little tricky
* Swapping words in a sentence
  * `I like chips and fish`
  * `fc` - cursor is now on `c` in `chips`
  * `de` - deletes `chips` and puts `chips` in unnamed register
  * `mm` - sets marker `m` on space before `and`
  * `ww` - gets to start of `fish`
  * `ve` - visually selects `fish`
  * `p` - replaces `fish` with `chips` and puts `fish` in unnamed register
  * `backtick+m` - goes back to `m` marker
  * `P` - puts `fish` in unnamed register in marker `m` location
* `gp` and `gP` commands that put text before or after the current line but leave cursor positioned at end of text instead of beginning
