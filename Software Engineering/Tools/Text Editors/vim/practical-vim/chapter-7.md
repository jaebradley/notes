# Chapter 7

## Using relative filepath

* `:pwd` Ex command
* `:edit {file}` command can accept a filepath relative to the working directory
  * If file to open is in same directory as active buffer can use `%` as shorthand for filepath of active buffer
  * `:edit %:h` - `:h` modified removes the filename while preserving the rest of the path
  * Can use `Tab` to autocomplete

## Using `netrw`

* set `set no compatible` and `filetype plugin on` in `vimrc` file
* `:edit .` - will open file explorer for current working directory
* `:Explore` - open file explorer for directory of active buffer
* To switch back to buffer out of file explorer `Ctrl-^` (or `:bd` to delete buffer, which file explorer is)

## Saving files to nonexistent directories

* When writing a file to a directory that doesn't yet exist, you need to `:!mkdir -p %:h`
* `mkdir -p` will create intermediate directories, while the `%:h` syntax will preserve path of buffer
