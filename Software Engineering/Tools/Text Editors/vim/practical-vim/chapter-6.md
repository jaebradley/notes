# Chapter 7

* Editing a file in vim is really editing an in-memory representation of a file, called a buffer
* Files are stored on disk, when a file is opened in Vim its contents are read into a bugger
* The `:ls` command gives listing of all buffers
* Can switch to next buffer by running `:bnext` and to previous buffer by running `:bprevious`
* Can switch to first buffer by running `:bfirst` and last buffer by running `:blast`
* The `%` symbol represents which buffers are visible in window while `#` represents alternative file
* Toggle between alternative files by pressing `Ctrl - ^`
* Can jump directly to a buffer using `:buffer N` (`:b N`)
* Delete a buffer by running `bdelete N1 N2 N3`

## Opening files in vim

* `*` symbol matches zero or more characters in the specified directory
* `**` matches zero or more characters recursively
* Can also build an args list like `:args **/*.js **/*.css`
* Can quit all files without reviewing unsaved changes with `:qall!`
* Can write all files without reviewing them with `:wall`

## Divide Workspace into Split Windows

* A window is a viewport into a buffer
* Divide window horizontally using `Ctrl-w-s` reusing current buffer
* Divide window vertically using `Ctrl-w-v` reusing current buffer
* Can use `:split {filename}` to divide workspace horizonally and open another buffer
* Can use `:vsplit {filename}` to divide workspace vertically and open another buffer
* `Ctrl-w+w` - cycle between windows
  * Can also do `Ctrl-w-w` (i.e. hold the control down)
  * Can do this with `j`, `k`, etc.
* `Ctrl-w+h` - focus window on left
* `Ctrl-w+j` - focus window below
* `Ctrl-w+k` - focus window above
* `Ctrl-w+l` - focus window right
* `Ctrl-w+c` - close active window
* `Ctrl-w+o` - keep only active window open
* `Ctrl-w+=` - equalize width and height of all windows
* `Ctrl-w+_` - maximize height of active window
* `Ctrl-w+|` - maximize width of active window
* `[N]+Ctrl-w+_` - Set active window height to N rows
* `[N]+Ctrl-w+|` - Set active window width to N columns

## Organize window layouts with tab pages

* A tab page is a container that can hold a collection of windows
* Can open a new tab page with `:tabedit`
* If current tab page has more than one window can use `Ctrl-w+T` to move window to new tag page
* `:tabc[lose]` - close current tab
* `:tabo[nly]` - keep active tab page, close all others
* `{N}gt` to switch between tabs (1-indexed)
  * If no number is specified goes to next tab
  * `gT` goes in reverse
* Can use `:tabmove [N]` to rearrage tab pages
  * When `N` is 0, tag page is moved to beginning
  * When `N` is omitted, tag page moves to end
