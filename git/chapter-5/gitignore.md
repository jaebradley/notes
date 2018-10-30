# `.gitignore`

## Formatting

* Impacts it's directory and all subdirectories
* blank lines are ignored
* lines starting with `#` are for comments
  * If a `#` exists anywhere other than the start of a line (i.e. it follows text) it does not indicate a comment
* A simple, literal filename matches a file in any directory with that name
* A directory name (i.e. has a trailing slash character) matches the named directory and any subdirectories
* Pattern containing shell globbing characters, like an `*` cannot extend across directories (i.e. an `*` can only match a single file or directory name)
* `!` inverts the following rule
  * Any file excluded by a previous rule but is included as part of an inversion is included
  * An inverted pattern overrides lower-precedence rules
    * This means you can override rules in a higher directory by including an inverted pattern in one of the subdirectories

## Hierarchy

* Patterns specified on the command line
* Patterns read from `.gitignore` in the same directory
* Patterns in parent directory
  * Current directory overrides parent's directory, which overrides their parent's directory
