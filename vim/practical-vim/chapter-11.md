# Chapter 11: Macros

## Recording a macro

* `q` key starts recording and then stops recording
  * `q{register}` records macro to defined register

```javascript
foo = 1
bar = 'a'
foobar = foo + bar
```

* Cursor at start of `foo`
* `qa` - records macro to register `a`
* `A;`, `Escape` - go to end of line, add `;` exit insert mode
  * `foo = 1;`
* `|` (pipe), `var`, `space`, `Escape` - go to beginning of line, type `var`, add a `space` and exit insert mode
  * `var foo = 1;`
* `q` - ends recording

## Playing back a macro

* `@{register}` command executes the contents of a specific register
* `@@` repeats the macro that was invoked most recently

## Normalize, Strike, Abort

* When recording a macro, ensure that every command is repeatable
* Normalize cursor position - ensure cursor is positioned so that next command has expected behavior
* Word-wise motions, like `w`, `b`, `e` tend to be more flexible than character-wise motions like `h` and `l`

## Play back with a count

* `11;.` does _not_ execute `;.` 11 times - instead it executes the `;` command eleven times and then the `.` command _once_
* Instead, can do `qq;.q`
  * `qq` - records keystrokes to `q` register
  * `;.` - command we want repeated
  * `q` to finish recording macro

## Repeat changes on contiguous lines

```bash
# Change this
1. one
2. two
3. three
4. four

# to this
1) One
2) Two
3) Three
4) Four
```

* `qa` - start recording macro to `a` register
* `0f.` - go to start of line and find first `.`
  * Using something like `f` instead of `l` is because if list was expanded to something like `10. ten` then `l` wouldn't work but `f` would
  * `f` also would raise an error if no `.` characters were found and macro would abort
* `r)` - replace `.` with `)`
* `w~` - go to start of next word and toggle case
* `j` - go to next line
* `q` - end recording

## Execute macro in parallel

* Same concept as before except there is a comment

```bash
1. one
2. two
// break up the monotony
3. three
4. four
```

* Same macro, except no `j` command to advance to the next line
* `jVG` after recording macro visually selects all lines after first line
* `:'<,'>normal @a` - execute macro once for each line in selection
* While macro will abort on comment, it won't prevent all other lines from being processed
  * All macro invokations are lined up "in parallel"

## Append commands to a macro

* `qa` - vim will overwrite register `a` with detected keystrokes
* `qA` - vim will append keystrokes to existing contents of register `a`
* Only useful for adding something to end of macro - cannot use this technique for adding something to beginning or middle of macro

## Executing a macro across many files

* Example is wrapping Ruby `class` in a `module`
* Can get all files for a given directory by doing something like `:cd path/to/directory`
* Then get all matching Ruby files by doing something like `:args *.rb`
  * Can navigate through files by doing `:first`, `:last`, `:prev`, and `:next`

```ruby
# Need to convert

class Animal
  # implementation
end

# to the following, which has a module wrapper

module Rank
  class Animal
    # implementation
  end
end
```

* `qa` - begin recording to register `a`
* `gg`, `/class`, `Return` - go to start of file and find first occurrence of `class`
* `O`, `module Rank`, `Escape` - Open new line above cursor, add `module Rank` and then exit insert mode
* `j>G` - Go to next line, indent every line to end of file
* `G`, `o`, `end`, `Escape` - go to end of file, add a line at end of file, and then add `end` text

### Executing the macro in parallel

* Using `:argdo` allows executing command once for each buffer in argument list (which is ruby files)
* However running `:argdo normal @a` would execute recently recorded macro on first file, which was just modified, thus applying the changes one too many times
* Need to revert changes made to the existing file using `:edit!`
* Now can apply macro against all files in argument list

## Insert numeric values that increment in a list

```bash
# Convert this

partridge in a pear tree
turtle doves
French hens
calling birds

# to this

1) partridge in a pear tree
2) turtle doves
3) French hens
4) calling birds
```

* `:let i=0` - creates a variable `i` set to `0`
* `:echo i` - `echo` inspects value assigned to variable
* `:let i += 1` - increments value of `i`
* `Ctrl+r` + `=` + `Return` - in *insert* mode this will insert value stored in variable `i`

### Macro

* `:let i = 1` - set variable `i` to `1`
* `qa` - start recording macro to register `a`
* `|` + `i` + `Ctrl + r` + `=` + `i` + `Return` + `)` + `space` + `Escape`
  * pipe starts at beginning of line
  * Enter insert mode
  * Insert value in variable `i`
  * Add `)`
  * Add `space` after `)`
  * Exit insert mode
* `:let i += 1` - increment `i` variable
* `q` - finish recording
