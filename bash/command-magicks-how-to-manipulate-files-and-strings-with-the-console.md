# [Command Magicks: How To Manipulate Files And Strings With The Console](https://medium.freecodecamp.org/command-magicks-how-to-manipulate-files-and-strings-with-the-console-3c554e64048)

## Commands

* `echo` followed by a string will simply make the console output what was given as input
* Calling the `cat` command on a file will output its contents into the terminal
  * `cat` takes shell-style names
  * For example, you can use wildcard characters, `*`, to match any string so `cat *.txt` will output the contents of all `txt` files
* `grep` takes a string and a filename
  * It searches for the string in the specified file
  * Outputs every line of the specified file where the string appears
  * `-i` ignores casing
  * filename also takes shell-style patterns, so you can do something like `grep "foo" *.txt` to `grep` against all `txt` files
* `sed` takes a file's contents and turns them into different ones
  * One common use case is to replace the parts of a string that matches a pattern, with different strings
  * `sed "s/some_regexp/replacement/{optional_flags}" filename`
    * Looks for every line that matches the defined `regexp`
    * Replaces the *first* `regexp` instance with `replacement`
    * Outputs the resulting string into the console (wihtout altering the file)
    * Supplying a `g` flag will match all instances on each line instead of just the first one
    * The `-i` argument will write to the input file

## Operators

* `|` - takes previous command's output and writes it to the following command's input
  * `cat *.txt | grep "is"` - fetches the contents of all text files, then searches for every line that contains `"is"`
* `>` - writes input to some output, usually a file
  * `echo "foobar" > foobar.txt` - quick way of creating a file (if it doesn't exist) called `foobar.txt` that contains `"foobar"`
* `>>` - writes input to output, but doesn't overwrite
  * `echo "foobar" >> foobar.txt` - appends `"foobar"` to `foobar.txt`
