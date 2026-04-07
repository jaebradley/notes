# [awk command](https://docs.rockylinux.org/10/books/sed_awk_grep/4_awk_command/)
* `awk` processes fields (columns) and records (rows)
* By default, `awk` treats each line of a file as a record and places these records in memory for line-by-line processing
  * A portion of each line is treated as a field in the record
* By default, delimiters to separate different fields use spaces and tabs, while numbers represent different fields in the row record
  * To reference multiple fields, separate them with commas or tabs
* `awk`'s usage is `awk option 'pattern {action}' filename`

## Options
* `-f` / `--file`: Read `awk` program source file
* `-F` / `FS`: Specify the separator for separating fields
  * `FS` is a built-in variable in `awk` - default value of spaces or tabs
* `-v {variable_name}={variable value}` 

## Patterns
* `BEGIN{}`: An action that is performed before all row records are read
* `END{}`: An action that is performed after all row records are read
* `/regular expression/`: Match the regular expression for each input line record
* `&&`: and operation
* `||`: or operation
* `!` : negation operation
* `pattern1,pattern2`: the pattern range to match all row records within that range

## `printf` commands
* `printf` formats and prints data with a usage like `printf FORMAT [ARGUMENT]...`

## Variables
* `FS`: delimiter of the input field (default is space or tab)
* `OFS`: delimiter of the output field (default is space)
* `RS`: delimiter of the input row record (default is newline character)
* `ORS`: delimiter of output row record
* `NF`: Count the number of fields in the current row record
* `NR`: Count the number of row records - after each line of text is processed, this increments by 1

## Examples
* These are the same: `awk 'BEGIN{FS=":"}{print $1}'` and `awk -v FS=":" '{print $1}'`
* `awk 'BEGIN{FS=":";OFS="\t"}{print $1,$2}'` or `awk -v FS=":" -v OFS="\t" '{print $1,$2}'` changes the output delimiter to a tab
* `awk -F ":" 'BEGIN{RS="\n";ORS="\n"} {print $(NF-2)}'` print the third-to-last field
* `awk -F ":" 'BEGIN{RS="\n";ORS="\n"} {print $NF}'` print the last field
* `awk 'END{print NR}'` total number of lines
* `awk 'NR==200'` : print the contents of line 200
* `awk 'BEGIN{RS="\n";ORS="\n"} NR<=10 {print NR,$0}'`  print content within a specific line range
* `awk '{if(NR==110) print $0}'` print a specific line
* `awk '{if(/^(ftp)\s|^(ssh)\s/) print $0}'` print lines that match a specific regex
* `awk '{if($0==10) print $0 ; else print "False"}'` both the truthy and falsy branches
* `awk '{print tolower($0)}'`  and `awk '{print toupper($0)}'`  to convert strings to lowercase / uppercase, respectively
