# Chapter 3

* In insert mode, can use these keystrokes to adjust without going to normal mode
  * `Ctrl - h` - go back one character
  * `Ctrl - w` - delete back one word
  * `Ctrl - u` - delete back to start of line
  * These commands also work in Vim's command line / bash shell
* Instead of presssing escape, can go back to normal mode by pressing `Ctrl - [`
* Ctrl - o` switches to Insert Normal mode where you can fire off single command and then be returned back to insert mode
* `zz` redraws screen with current line in middle of windows
  * Can combine with `Ctrl - o` (i.e. Insert Normal mode` to read half screen above and below
* Remapping caps lock key
  * Escape key is hard to reach 
  * Prefer to map caps lock to Ctrl since `Ctrol - [` is synonymous with escape

## Example of Pasting From Register

* Let's say there's some text (up until a comma) that we want to copy
* `yt,` (note the comma) yanks until a comma into the yank register
* Insert mode press `Ctrl - r + 0` to paste text that was just yanked into cursor position
  * `Ctrl - r` {register}` - register is address of register

## Expression Register

* Expression register address is `=`
* In insert mode, can access it via `Ctrl - r + =`
* When done evaluating expression press enter

# Overwriting with Replace Mode

* From normal mode, `R` to engage replace mode
* Tab character is single character in file but expands to fill several columns
  * Can lead to unexpected behavior when overwriting as tab character would get overwridden and would have an impact visually
  * `gR` will treat tab character as if it consisted of spaces
* `r{char}` and `gr{char}` allow overwrite of single character before switching back to Normal mode

