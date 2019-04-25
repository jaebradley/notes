# [Thirty Basic Vim Commands You'll Use Everyday](https://spin.atomicobject.com/2016/04/19/vim-commands-cheat-sheet/)

* `B` / `W` - move backwards or forwards by word ignoring whitespace
* `0` - start of line
* `^` - first non-whitespace character
* `$` - end of line
* `CTRL + u` - Page Up
* `CTRL + d` - Page Down
* `H` - move to top of screen
* `M` - move to middle of screen
* `L` - move to end of screen
* `#` - find previous occurence of token under cursor
* `*` - find next occurrence of token under cursor
* two backticks - jump back to where you were
* `n` - repeat last find command forward
* `N` - repeat last find command backward
* `I` - insert at beginning of line
* `A` - append at end of line
* `s` - Delete character at cursor and edit insert mode
  * `cw` is usually faster
* `ctrl + r` - redo
* `.` - Repeat previous edit command

## Correcting Text

* `t<char>` - continue up to (but not including) the next `<char>` on this line
* `f<char>` - continue up to AND include the next `<char>` on this line
  * `cf)` - will correct all text up to and including the next `)` on the line
* `i<char>` - apply some operation to text bounded by `<char>` where `<char>` is from a limited set of characters that come in pairs (quotes, parentheses, brackets)
  * `ci(` - search backward to find first open paren, then forward to find its match, then correct text between (but not including characters)
  * prepend a number like `2ci` to break out of inner parentheses
* `a<char>` - same as `i<char>` except it also applies to th `<char>`s on either end
