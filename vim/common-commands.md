# [Common Commands](https://stackoverflow.com/a/5400978/10039741)

## Movement

* `w` / `W` next word
* `b` / `B` back word
* `e`/ `E` end word
* `{` / `}` previous / next blank line
* `mx` to set marker `x` / `'x` to go back to marker `x`
* `CTRL + F` to go forward full screen
* `CTRL + B` to go backwards full screen
* `CTRL + D` to go down half screen
* `CTRL + U` to go up half screen
* `CTRL + E` to go up by a line
* `CTRL + Y` to go down by a line

## Editing

* `u` undo
* `CTRL + R` redo
* `.` repeat last editing command

## Inserting

* `i` insert text at cursor
* `I` insert text at beginning of line
* `a` append text after cursor
* `A` append text after end of line
* `r` replace single character
* `R` replace multiple characters
* `s` change single character
* `cw` to change word
* `C` to change to end of line
* `cc` to change whole line
* `c<motion>` to change in that motion so `cb` changes text back a word
* `ci(` changes text inside parenthesis

## Deleting

* `x` to delete character
* `dw` to delete word
* `D` to delete end of line
* `dd` to delete whole line (also copies to paste buffer)
* `d<motion>` to delete in motion

## Cut / Copy

* `yy` to copy line
* `p` paste below
* `P` paste above
* `xp` swaps two characters (`x` deletes the character and puts it into buffer and then `p` pastes after cursor)

## Find / replace

* `%s/foo/bar/g`
  * `%s` sets the range - in this case it indicates every line in file
  * `/g` indicates that every instance of `foo` will be replaced with `bar` instead of just first instance

## Files

* `ZZ` write file to disk and quit
* `:e .` directory explorer

## Searching

* `/` search forward
* `?` search backwards
* `*` search forward word under cursor
* `#` search backwards word under cursor
* `n` next match in same direction
* `N` next match in opposite direction
* `%` matching parentheses
* `fx` move to next `x`
* `Fx` move to previous `x`

## Registers

* `"+yy` to copy line to system copy / paste
* `"+p` to paste from system copy / paste
* `"k` to paste to register `k`
