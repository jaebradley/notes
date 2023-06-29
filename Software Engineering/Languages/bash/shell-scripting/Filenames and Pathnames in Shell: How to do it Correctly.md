# [Filenames and Pathnames in Shell: How to do it Correctly](https://dwheeler.com/essays/filenames-in-shell.html)

## Prefix all globs so they cannot exapnd to begin with "-"

* Do not begin with a globbing character (like `*`)
* If globbing from the current directory, prefix the glob with `"./"`
* Almost all commands will interpret a string beginning with a `-` as an option, and not as a filename, until the command sees something that does not begin with a `-`
* Globs are expanded by the shell into a list of filenames, and `-` is earlier in alphanumeric sort order, so attackers can make sure that a pathname starting with a  `-` is executed first
* If pathnames are prefixed, then any pathnames starting with `-` will be handled correctly
