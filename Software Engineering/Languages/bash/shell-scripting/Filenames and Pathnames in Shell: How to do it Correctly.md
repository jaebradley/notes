# [Filenames and Pathnames in Shell: How to do it Correctly](https://dwheeler.com/essays/filenames-in-shell.html)

## How to do it wrongly

### `cat * > ../collection`

* If a filename in the current directory begins with "-", it will be misinterpreted as an option instead of as a filename
  * A file named `-n` will enable `cat`'s `-n` option instead
  * Also prefix globs with `./`
 
### `cat $(find . -type f) > ../collection`

* Any pathname containing a whitespace character will be split
* file `a b` will be split into files `a` and `b`
* If `find` does not match, `cat` may wait for input from standard input
  * Fixed by appending `/dev/null` pathname

## Prefix all globs so they cannot exapnd to begin with "-"

* Do not begin with a globbing character (like `*`)
* If globbing from the current directory, prefix the glob with `"./"`
* Almost all commands will interpret a string beginning with a `-` as an option, and not as a filename, until the command sees something that does not begin with a `-`
* Globs are expanded by the shell into a list of filenames, and `-` is earlier in alphanumeric sort order, so attackers can make sure that a pathname starting with a  `-` is executed first
* If pathnames are prefixed, then any pathnames starting with `-` will be handled correctly
