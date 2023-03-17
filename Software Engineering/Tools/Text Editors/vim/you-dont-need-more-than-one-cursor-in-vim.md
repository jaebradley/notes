# [You don't need more than one cursor in vim](https://medium.com/@schtoeffel/you-don-t-need-more-than-one-cursor-in-vim-2c44117d51db)

* Select and edit in one step
  * `/<your search input>`
  * `cgn` - change next found occurrence
  * Use `.` to apply to next word
  * Use `n` to skip and jump to next word
  * Uses `gn` text-object - use `d` to delete matches instead of changing them
* Using visual select and macros to change multiple lines
* Imagine changing a bunch of `const x = require('x');` into `import x from 'x';`
* `qqce`
  * `q` tells vim to begin recording a macro
  * The next `q` tells vim to store the macro in the `q` register
  * `ce` - deletes characters from current cursor position to end of word
* type `import`
* `escape` key
* `f=` - jump to next `=` on line
* `cf(from<space><esc>f)x`
  * `cf(` - delete until `(`
  * Insert mode and type `from<space>`
  * `escape` to exit insert mode
  * `f)` - jump to next `)`
  * `x` delete `)` - `x` deletes characters under and after cursor (if a count is passed to it)
* `q` to finish recording
* Go to each line and `@q` should apply the recording to each line

