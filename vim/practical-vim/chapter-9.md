# Chapter 9: Navigate Between Files with Jumps

* `Ctrl+o` moves back through jump list
* `Ctrl+i` moves forwards through jump list
* motions move around within file, jumps move between files
* `:jumps` shows contents of jump list
* Different jumps are
  * `[count]G` - jump to line number
  * `/pattern/*` - jump to next/previous occurrence of pattern
  * `%` - jump to matching parenthesis
  * `(` / `)` - jump to start of previous/next sentence
  * `{` / `}` - jump to start of previous/next paragraph
  * `H` / `M` / `L` - jump to top/middle/bottom of screen
  * `gf` - jump to file name under the cursor
  * `Ctrl+]` - jump to definition of keyword under cursor
  * `'{mark}` - jump to a mark
* Vim maintains a list of modifications made to each buffer during course of editing session
* `:changes`
* `g;` traverses backwards through change list
* `g,` traverses forwards through change list

## Opening the file under cursor

* `gf` opens file under cursor
* Need to specify file extension
* `suffixesadd` option allows specifying one or more file extensions that Vim will use when looking up filenames with `gf`
* Setting the `path` option to reference a comma-separated list of directories
  * Vim will check directories in `path` to see if it contains filename that matches

## Global Marks

* `mV` sets a global mark for `V`
* `backtick+V` will open file for global mark set by `mV`
* Get in habit of setting global mark before using commands that interact with quickfix list like `:grep`, `:vimgrep`, etc.

