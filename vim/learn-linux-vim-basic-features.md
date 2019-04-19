# [Why I Love Vim: it's the lesser-known features that make it so amazing](https://medium.freecodecamp.org/learn-linux-vim-basic-features-19134461ab85)

## Tab pages

* `:tabedit file2` opens a new tab and takes you to `file2` in edit mode
* `:gt` takes you to next tab
  * `:3gt` takes you to third tab (`1`-indexed)
* `:gT` takes you to previous tab
* `:tabs` to see all open tabs
* `vim -p file1 file2` opens multiple files in tabs
* `:tabclose` to close a single tab
* `:tabonly` to close all tabs except current one

## Tab sessions

* `:mksession some-session-name.vim` will store session with open tabs
* `vim -S some-session-name.vim` will reopen tabs

## Cut / Copy without line numbers

* Use markers to, well, mark the beginning and end of block
* `mk` - will mark start with `k` (can use any letter for marker)
* Move down to end and then
  * `y'k` will copy everything from start to end
  * `d'k` will cut everything from start to end

## Code folding

* Mark a spot with `mb`
* Move to end of function or end of block you'd like to fold
* `zf'b` will fold up that block of code
* `zo` will open a code fold
* `zc` will close up a code fold


