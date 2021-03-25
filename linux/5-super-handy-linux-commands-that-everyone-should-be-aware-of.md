# [5 Super Handy Linux Commands That Everyone Should Be Aware Of](https://levelup.gitconnected.com/5-super-handy-linux-commands-that-everyone-should-be-aware-of-a70b08d2c635)

* `tee` copies standard input to standard output
  * Helpful when redirecting data to a file as well as seeing what's in the file
  * `cat some_file | tee some_other_file` - output contents of a file, the `tee` program reads the contents and writes the contents to standard out and `some_other_file`
* `fc` allows editing a long command in an editor (like `vim`)
