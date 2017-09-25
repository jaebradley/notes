# Commandline Challenge


## `hello_world`
  * Print 'hello world'
  * `echo 'hello world'`

## `current_working_directory`
  * Print the current working directory
  * `pwd`

## `list_files`
  * List names of all the files in the current directory, one file per line
  * `ls`

## `print_file_contents`
  * Print the contents of a file named `access.log`
  * `cat access.log`

## `last_lines`
  * Print the last 5 lines of `access.log`
  * `tail access.log -n 5`

## `find_string_in_a_file`
  * Print all lines in the `access.log` file that contains the string `GET`
  * `grep 'GET' access.log`

## `search_for_files_containing_string`
  * Print all files in the current directory that contain the string `500`
  * `grep '500' * -l`

## `search_for_files_by_extension`
  * Print the relative file paths, one path per line for all filenames that start with `access.log` in the current directory
  *  `find . -name 'access.log*'`

## `search_for_string_in_files_recursive`
  * Print all matching lines (without the filename or the file path) in all files under the current directory that start with `access.log` that contain the string `500`.
  * `find . -name 'access.log*' | xargs grep -l '500'`

## `extract_ip_addresses`
  * Extract all IP addresses from files that start with `access.log` printing one IP address per line.
  * `find . -name 'access.log*' | xargs grep -Eo '^[^ ]+'`

## `delete_files`
  * Delete all of the files in this challenge directory including all subdirectories and their contents
  * `find -delete`

## `count_files`
  * Count the number of files in the current working directory. Print the number of files as a single integer.
  * `ls -ap | grep -v / | wc -l`
    * `-a` includes entries that start with a `.`
    * `-p` prefaces folders with a `/`
    * `-v` inverts the match - in this case, finding all values without a `/` (so excluding files)
    * `wc -l` counts the lines in the previous output

## `simple_sort`
  * Print the contents of access.log sorted.
  * `sort access.log`

## `count_string_in_line`
  * Print the number of lines in `access.log` that contain the string `GET`.
  * `grep 'GET' access.log | wc -l`

## `split_on_a_char`
  * The file `split-me.txt` contains a list of numbers separated by a `;` character. Split the numbers on the `;` character, one number per line.
  * `sed 's/;/\n/g' split-me.txt`
  * `sed` is the `Stream EDitor` - most commonly used for text replacement
  * The `s` stands for substitute
  * The `g` means to globally replace on each line, or else it would only update the first match

## `print_number_sequence`
  * Print the numbers `1` to `100` separated by spaces
  * `seq -s ' ' 1 100`
  * `seq` prints a sequence of numbers
  * The `-s` splits the numbers by some string. `\n` is the default.

## `replace_text_in_files`
  * There are text files (`.txt` extension) that contain the phrase `challenges are difficult`. Delete this phrase recursively from all text files.
  * `find . -name '*.txt' -exec sed -i 's/challenges are difficult//g' {} +`

## `sum_all_numbers`
  * The file `sum-me.txt` has a list of numbers, one per line. Print the sum of these numbers.
  * `cat sum-me.txt | xargs | sed 's/ /+/g' | bc`
  * Reads from file
  * `xargs` takes the input and space-delimits the text
  * `sed` replaces the space with `+`s
  * `bc` executes the expression, which is a sum

## `just_the_files`
  * Print all files in the current directory recursively without the leading directory path
  * `find . -type f | xargs -n 1 basename`
  * Find all files
  * `-n` splits the output with 1 item per line
  * `basename` gets the basename of the file

## `remove_extensions_from_files`
  * Rename all files removing the extension from them in the current directory recursively.
  * `find . -type f | while read f; do mv "$f" "${f%.*}"; done`
