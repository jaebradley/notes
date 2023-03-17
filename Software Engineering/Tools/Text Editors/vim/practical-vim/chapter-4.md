# Chapter 4

* Change `March` to `April` in visual model
  * `viw` (to visually select word)
  * `c` to change the selection (can't use `A` because that would append text
* 3 ways to visually select things - characters, lines or blocks
* From normal mode, press `v` to enable character-wise visula mode
* Enable line-wise visual mode with `V`
* Enable block-wise visual mode with `Ctrl - v`
* `gv` reselects last range of text that was selected through visual mode
* `o` go to end of highlighted text
* `e` go to beginning of highlighted text

## Visual Operator

* Can select the contents of a tag (like HTML tag) using `vit` (i.e. visually select inside the tag)
* Could uppercase selections using `U` command
* Could try and repeat using dot operator
* When tag contents have a different length, the visual mode command will only affect the same range of text
* Instead use `gUit`
  * Can be considered one command consisting of an operator (`gU`) and a motion (`it`)
  * `vitU` is considered two commands
    * `vit` to make selection and `U` to transform

## Edit Tabular Data with Visual-Block Mode

* Can go from basic table with columns to something like

```bash
Chapter         |    Page
-------------------------
Normal mode     |    15
Insert mode     |    31
Visual mode     |    44 
```

* `Ctrl - v` to engage visual block mode
* Define columns selected by `3j`
* For each of these columns we want to delete some spaces so `x...`
* `gv` will reselect the previous visual block
* `r|` will replace with `|`
* `yyp` will yank header and past it below in another line
* `Vr-` will visually select line and replace all characters with `-`

## Change Columns of Text

* Change all the image URLs to have a `components` directory

```css
li.one   a{ background-image: url('/images/sprite.png'); } 
li.two   a{ background-image: url('/images/sprite.png'); } 
li.three a{ background-image: url('/images/sprite.png'); } 
```

* This only works because they have the same spacing / are in the same "column" of text
* Start at beginning of first `images`
* `Ctrl - v` to enter visual mode
* `jje` to select next two lines and then `e` to go to end of word
* `c` to remove content and insert
* typing `components` will overwrite the first `images` - exiting to normal mode will replace remaining selections

```css
li.one   a{ background-image: url('/components/sprite.png'); } 
li.two   a{ background-image: url('/components/sprite.png'); } 
li.three a{ background-image: url('/components/sprite.png'); } 
```

## Append after each line

```javascript
var foo = 1
var b = 'a'
var foobar = foo + bar
```

* `Ctrl - v` to go to visual block mode
* `jj$` to select next two lines and go to end
* `A;` to append `;` to end of line (will only affect first one)
* Go to normal mode to affect all lines

```javascript
var foo = 1
var b = 'a'
var foobar = foo + bar
```
