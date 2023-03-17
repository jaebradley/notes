# [How not to be afraid of Vim anymore](https://medium.com/free-code-camp/how-not-to-be-afraid-of-vim-anymore-ec0b7264b0ae)

* Commands in Vim follow a set pattern
  * [action] <number> [motion]
  * action - what you want to do
  * motion is the range of that action
* Delete three words - `d3w`
  * action is `delete`
  * number of things to delete is `3`
  * the "motion" is a "word"
* Common Actions
  * `d` - delete
  * `i` - insert
  * `p` - paste
  * `y` - yank
  * `x` - cut
  * `u` - undo
  * `di` / `yi` - delete inside / yank inside
  * `v` - visual mode
  * `/` - search
  * `%` - parentheses matching
  * `:s` - substitue
* Common Motions
  * `w` - beginning of next word
  * `e` - end of current word
  * `b` - beginning of previous word
  * `$` - end of line
  * `0` - beginning of line
  * `G` - end of file
  * `)` - jump forward a sentence
  * `}` - jump forward a paragraph
  * Closing brackets go backwards
* `:tabnew <filename>` opens new file in new tab
  * `:tabn` to go to next tab
  * `:tabp` to go to previous tab

